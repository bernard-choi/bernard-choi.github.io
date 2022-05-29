---  
layout: post  
title: "airflow tips"  
subtitle: "airflow tips"  
categories: Development
tags: Airflow Python
comments: true  


---  
crontab으로 돌고있는 회사의 배치작업을 airflow로 전환시키려고 합니다. airflow 도입을 통해 얻을 수 있는 장점은 다음과 같습니다.

- **모니터링 기능 강화**
  - 로그파일을 열어보지 않아도 웹 ui를 통해 배치가 돌았는지 확인 가능
  - 배치가 문제 생길 시 메일링 or slack maessage

- **web ui에서 바로바로 실행이 가능**
  - crontab의 경우 파이썬 파일을 실행시켜야 하지만 airflow는 클릭만으로 실행이 가능

- **복잡한 dependency를 간단하게 구현이 가능**
  - A작업이 끝나고 B와 C작업이 실행, B와 C작업이 모두 끝나야 D작업이 실행되어야 할때 이를 간단하게 구현이 가능
  - A > [B,C] > D

airfow에 대한 다양한 팁들이 정리된 Medium글을 번역해보았습니다.

## (1) DAG with context Manager

operator 설정에서 `dag=dag`를 반복해서 적어주기 보다는 with 구문을 쓰는 context_manager를 사용하는 것이 좋습니다.

```python
# Normal DAG without Context Manager
args = {
  'owner': 'yunsikus'
  'start_date': airflow.utils.dates.days_ago(2)
}
dag = DAG(
    dag_id='example_dag',
    default_args=args,
    schedule_interval='0 0 * * *',
)

run_this_last = DummyOperator(
    task_id='run_this_last',
    dag=dag,  # You need to repeat this for each task
)

run_this_first = BashOperator(
    task_id='run_this_first',
    bash_command='echo 1',
    dag=dag,  # You need to repeat this for each task
)

run_this_first >> run_this_last
```

```python
# DAG with Context Manager
args = {
    'owner': 'yunsikus',
    'start_date': airflow.utils.dates.days_ago(2),
}

with DAG(dag_id='example_dag', default_args=args, schedule_interval='0 0 * * *') as dag:

	run_this_last = DummyOperator(
	    task_id='run_this_last'
	)

	run_this_first = BashOperator(
	    task_id='run_this_first',
	    bash_command='echo 1'
	)

	run_this_first >> run_this_last
```

위의 예시에서는 2개의 task가 있지만, 10개 혹은 그 이상이 생기면 불필요한 중복이 생기게 됩니다. 이를 피하기 위해 with 구문을 이용한 context managers를 사용할 수 있습니다.

## (2) Using List to set Task dependencies

아래와 같은 이미지의 DAG를 실행 시, task dependency를 적용시키기 위해 task를 계속 반복해야하는 불편함이 있습니다.

![airflow_medium1](https://yunsikus.github.io/assets/img/post_img/airflow_medium1.jpg)

```python
task_one >> task_two
task_two >> task_two_1 >> end
task_two >> task_two_2 >> end
task_two >> task_two_3 >> end

# Using Lists (being a PRO :-D )
task_one >> task_two >> [task_two_1, task_two_2, task_two_3] >> end
```

위의 코드서 보여지는 것처럼 일반적인 방법으로는 task_two가 끝나고 end까지 3번 반복됩니다. 이를 리스트를 통해 한줄로 줄일 수 있습니다.

## (3) Use default arguments to avoid repeating arguments

 Airflow 에서는 parameter의 묶음들을 (dictionary형태) 모든 task에서 공유하도록 하는 기능을 제공합니다. 에를 들어 일일이 task마 `labels`, `bigquery_conn_id`를 정해주기 보다는 `default_args`에 적으면 중복을 줄일 수 있습니다.

 ```python
 default_args = {
     'owner': 'airflow',
     'depends_on_past': False,
     'start_date': airflow.utils.dates.days_ago(2),
     # All the parameters below are BigQuery specific and will be available to all the tasks
     'bigquery_conn_id': 'gcp-bigquery-connection',
     'write_disposition': 'WRITE_EMPTY',
     'create_disposition': 'CREATE_IF_NEEDED',
     'labels': {'client': 'client-1'}
 }

 with DAG(dag_id='airflow_tutorial_gcp', default_args=default_args, schedule_interval=None) as dag:

     query_1 = BigQueryOperator(
         task_id='query_1',
         sql='select 1'
     )

     query_2 = BigQueryOperator(
         task_id='query_2',
         sql='select 1'
     )

     query_1 >> query_2
 ```

## (4) The "Params" argument

"params"는 Dag 단계에서 딕셔너리 형태로 적용할 수 있으며, task단계에서 overwritten 됩니다.

```python
# You can pass `params` dict to DAG object
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': airflow.utils.dates.days_ago(2),
}

dag = DAG(
    dag_id='airflow_tutorial_2',
    default_args=default_args,
    schedule_interval=None,
    params={
        "param1": "value1",
        "param2": "value2"
    }
)

bash = BashOperator(
    task_id='bash',
    bash_command='echo {{ params.param1 }}', # Output: value1
    dag=dag
)
```

`default_args` 단계에서도 'params'를 넘길 수 있으 `dag`단계와 `operator` 단계에서 상속이 가능합니다.

```python
# You can override `params` dict passed in DAG object in `default_arg` dict
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': airflow.utils.dates.days_ago(2),
    'params': {
        "param1": "value2",
        "param2": "value2"
    }
}

dag = DAG(
    dag_id='airflow_tutorial_2',
    default_args=default_args,
    schedule_interval=None,
    params={
        "param1": "value1",
        "param2": "value2"
    }
)

# You can override `params` dict passed in DAG object or `default_arg` in each individual tasks
bash = BashOperator(
    task_id='bash',
    bash_command='echo {{ params.param1 }}', # Output: value3
    params={
        "param1": "value3"
    }
    dag=dag
)
```
이를 통해 주요 parameter들을 하드코딩하지 않고 더 수월하게 관리할 수 있게 됩니다. 또한, 위의 예시들 처럼 params dictionary는 3 장소에서 사용할 수 있습니다. `(1)DAG단계`, `(2)default_args`, `(3)task`

## (5) Storing Sensitive data in Connections

python script안에 connection data를 관리하기 보다는 airflow의 metadata db에 기록하자. `crypto` 패키지를 통해 암호화하여 관리 가능하다.

## (6) Restrict the number of Airflow variables in your dag

Airflow Variables는 메타데이터 db에 저장되어있으며 variable을 호출할때마다 db에 커넥션을 날려야 합니다. 많은 종류의 variable을 사용한다면 그만큼 커넥션을 날리는 작업으로 인한 과부하가 예상됩니다. 이를 예방하기 위헤 json으로 여러개의 parameter를 묶어주는 방법이 있습니다. 한번의 호출로 여러개의 parameter들을 가지고 올 수 있습니다.

![airflow_medium2](https://yunsikus.github.io/assets/img/post_img/airflow_medium2.jpg)

```python
from airflow.models import Variable

# Common (Not-so-nice way)
# 3 DB connections when the file is parsed
var1 = Variable.get("var1")
var2 = Variable.get("var2")
var3 = Variable.get("var3")

# Recommended Way
# Just 1 Database call
dag_config = Variable.get("dag1_config", deserialize_json=True)
dag_config["var1"]
dag_config["var2"]
dag_config["var3"]

# You can directly use it Templated arguments {{ var.json.my_var.path }}
bash_task = BashOperator(
    task_id="bash_task",
    bash_command='{{ var.json.dag1_config.var1 }} ',
    dag=dag,
)

```
## (7) The "context" dictionary

Python Operator에는 python 함수 호출 시 해당 함수에서 사용될 수 있는 기본적인 argument 값을 넘겨주는 기능이 있다. `provide_context = True`를 설정해주면  다음과 같은 context dictionary가 python 함수로 넘어간다.

```python
{
      'dag': task.dag,
      'ds': ds,
      'next_ds': next_ds,
      'next_ds_nodash': next_ds_nodash,
      'prev_ds': prev_ds,
      'prev_ds_nodash': prev_ds_nodash,
      'ds_nodash': ds_nodash,
      'ts': ts,
      'ts_nodash': ts_nodash,
      'ts_nodash_with_tz': ts_nodash_with_tz,
      'yesterday_ds': yesterday_ds,
      'yesterday_ds_nodash': yesterday_ds_nodash,
      'tomorrow_ds': tomorrow_ds,
      'tomorrow_ds_nodash': tomorrow_ds_nodash,
      'END_DATE': ds,
      'end_date': ds,
      'dag_run': dag_run,
      'run_id': run_id,
      'execution_date': self.execution_date,
      'prev_execution_date': prev_execution_date,
      'next_execution_date': next_execution_date,
      'latest_date': ds,
      'macros': macros,
      'params': params,
      'tables': tables,
      'task': task,
      'task_instance': self,
      'ti': self,
      'task_instance_key_str': ti_key_str,
      'conf': configuration,
      'test_mode': self.test_mode,
      'var': {
          'value': VariableAccessor(),
          'json': VariableJsonAccessor()
      },
      'inlets': task.inlets,
      'outlets': task.outlets,
}
```

## (8) Generating Dynamic Airflow Tasks

task id를 유일하게 설정하여 다이내믹한 task를 설계할 수 있습니다.

```python
# Using DummyOperator
a = []
for i in range(0,10):
    a.append(DummyOperator(
        task_id='Component'+str(i),
        dag=dag))
    if i != 0:
        a[i-1] >> a[i]

# From a List
sample_list = ["val1", "val2", "val3"]
tasks_list = []
for index, value in enumerate(sample_list):
    tasks_list.append(DummyOperator(
        task_id='Component'+str(index),
        dag=dag))
    if index != 0:
        tasks_list[index-1] >> tasks_list[index]
```

출처

[airflow-lesser-known-tips-tricks-and-best-practises](https://medium.com/datareply/airflow-lesser-known-tips-tricks-and-best-practises-cf4d4a90f8f)
