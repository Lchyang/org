
# Table of Contents

1.  [celery](#org378f1d9)
    1.  [使用场景](#org2ee9313)
    2.  [Celery 基本使用](#org1ec8212)
    3.  [Celery 配置文件](#orgf08eb6f)
    4.  [定时任务](#org4e757e1)
    5.  [Django 中使用celery](#org799aecf)


<a id="org378f1d9"></a>

# celery

celery 是一个简单、灵活且可靠的、处理大量消息的分布式系统
专注于实时处理异步任务队列
同时也支持任务调度


<a id="org2ee9313"></a>

## 使用场景

异步任务： 将耗时操作任务提交给celery去异步执行，比如发送短信/邮件、消息推送、
音视频处理等
定时任务： 类似于crontab， 比如每日数据统计


<a id="org1ec8212"></a>

## Celery 基本使用

tasks.py

    import time
    from celery import Celery
    
    
    broker = 'redis://localhost:6379/1'
    backend = 'redis://localhost:6379/2'
    app = Celery('my_stacks', broker=broker, backend=backend)
    
    
    @app.task
    def add(x, y):
        print('enter call func..')
        time.sleep(4)
        return x + y

test<sub>celery.py</sub>

    from tasks import add
    
    if __name__ == '__main__':
        print('start task...')
        result = add.delay(2, 8)
        print('end stask... ')
        print(result)

基本命令

    In [1]: from tasks import add
    
    In [2]: add.delay(2,8)
    Out[2]: <AsyncResult: d368f92c-a938-4ec3-aa28-bb046275d948>
    
    In [3]: result = add.delay(2,9)
    
    In [4]: result.ready()
    Out[4]: True
    
    In [5]: result.get()
    Out[5]: 11


<a id="orgf08eb6f"></a>

## Celery 配置文件

celery<sub>app</sub>.\_<sub>init</sub>\_<sub>.py</sub>

    from celery import Celery
    
    
    app = Celery('demo')
    # 通过 celery 实例加载配置模块
    app.config_from_object('celery_app.celeryconfig')

celery<sub>app.celeryconfig.py</sub>

    BROKER_URL = 'redis://localhost:6379/1'
    
    CELERY_RESULT_BACKEND = 'redis://localhost:6379/2'
    
    CELERY_TIMEZONE = 'Asia/Shanghai'
    
    # 导入制定模块
    CELERY_IMPORTS = (
        'celery_app.task1',
        'celery_app.task2'
    )

celery<sub>app.task1.py</sub>

    import time
    
    from celery_app import app
    
    
    @app.task
    def add(x, y):
        time.sleep(3)
        return x + y

celery<sub>app.task2.py</sub>

    import time
    
    from celery_app import app
    
    
    @app.task
    def multiply(x, y):
        time.sleep(4)
        return x * y

test<sub>celery.py</sub>

    from celery_app import task1, task2
    
    
    task1.add.delay(2, 4)
    task2.multiply.delay(4, 5)
    print('end...')

通过 celery worker -A celery<sub>app</sub> -l INFO 命令启动worker
然后运行 python test<sub>celery.py</sub> 


<a id="org4e757e1"></a>

## 定时任务

      from datetime import timedelta
      from celery.schedules import crontab
    
      BROKER_URL = 'redis://localhost:6379/1'
    
      CELERY_RESULT_BACKEND = 'redis://localhost:6379/2'
    
      CELERY_TIMEZONE = 'Asia/Shanghai'
    
      # 导入制定模块
      CELERY_IMPORTS = (
          'celery_app.task1',
          'celery_app.task2'
      )
    
    #定时任务
      CELERYBEAT_SCHEDULE = {
          'task1': {
              'task': 'celery_app.task1.add',
              'schedule': timedelta(seconds=10),
              'args': (2, 8),
          },
          'task2': {
              'task': 'celery_app.task2.multiply',
              'schedule': crontab(hour=19, minute=14),
              'args': (4, 5)
          }
      }

启动 任务
celery worker -A celery<sub>app</sub> -l INFO
celery beat -A celery<sub>app</sub> -l INFO


<a id="org799aecf"></a>

## Django 中使用celery

pip install django-celery 

在setting同目录下创建celeryconfig.py文件

    from datetime import timedelta
    import djcelery
    
    djcelery.setup_loader()
    
    CELERY_QUEUES = {
        # 定时任务
        'beat_tasks': {
            'exchange': 'beat_tasks',
            'exchange_type': 'direct',
            'binding_key': 'beat_tasks',
        },
        # 普通任务
        'work_queue': {
            'exchange': 'work_queue',
            'exchange_type': 'direct',
            'binding_key': 'work_queue',
        }
    }
    
    # 默认任务
    CELERY_DEFAULT_QUEUE = 'work_queue'
    
    
    CELERY_IMPORTS = (
        'blog.tasks',
    )
    
    # 有些情况可以防止死锁
    CELERYD_FORCE_EXECV = True
    
    # 设置并发的worker数量
    CELERYD_CONCURRENCY = 4
    
    # 允许重试
    CELERY_ACKS_LATE = True
    
    # 每个worker最多执行100个任务被销毁，可是防止内存泄漏
    CELERYD_MAX_TASKS_PER_CHILD = 100
    
    #单个任务的最大运行时间
    CELERYD_TASK_TIME_LIMIT = 12 * 30
    
    # 定时任务
    CELERYBEAT_SCHEDULE = {
        'task1': {
            'task': 'course_task',
            'schedule': timedelta(seconds=5),
             # 'args': ()
            'options': {
                'queue': 'beat_tasks'
            }
        }
    }

在app中注册 djcelery 在setting.py 中增加

    #celery
    from .celeryconfig import *  #使celeryconfig 和setting联系
    # redis 配置
    BROKER_BACKEND = 'redis' 
    BROKER_URL = 'redis://localhost:6379/1'
    CELERY_RESULT_BACKEND = 'redis://localhost:6379/2'

在应用下创建task.py文件

    import time
    
    from celery.task import Task
    
    
    class CourseTask(Task):
        name = 'course_task'
    
        def run(self, *args, **kwargs):
            print('start course task')
            time.sleep(4)
            print('args={}, kwargs={}'.format(args, kwargs))
            print('end course task')

在views中

    from django.http import JsonResponse
    
    from blog.tasks import CourseTask
    
    
    def do(request):
        # 执行异步
        print('start do request')
        CourseTask.delay()
        print('end do request')
        return JsonResponse({'result': 'ok'})

配置url访问views
运行时 python managge.py runserver,   python manage.py celery worker -l INFO
运行定时任务要加上  python manage.py celery beat -l INFO

