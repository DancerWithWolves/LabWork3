# LabWork3# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил:
- Кудрин Владислав Дмитриевич
- НМТ-213901
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)



## Цель работы
Ознакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity

## Задание 1
### Задание 1. Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity. 
Ход работы:
-	Создать новый пустой 3D проект на Unity.
-	Скачать папку с ML агентом.
-	В созданный проект добавить ML Agent, выбрав Window - Package Manager - Add Package from disk. Последовательно добавьте .json – файлы:
o	ml-agents-release_19 / com,unity.ml-agents / package.json
o	ml-agents-release_19 / com,unity.ml-agents.extensions / package.json
-	Если все сделано правильно, то во вкладке с компонентами (Components) внутри Unity вы увидите строку ML Agent.
-	Запустить Anaconda Prompt для возможности запуска команд через консоль.
-	Пишем серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек:
o	mlagents 0.28.0;
o	torch 1.7.1;


![Снимок1](https://user-images.githubusercontent.com/114332610/198073325-4868537e-8ee8-4a35-b685-e57595aa294a.PNG)
Рисунок 1

![Снимок2](https://user-images.githubusercontent.com/114332610/198073365-3bf739a5-ff3a-4c8a-93b8-5ecabe695966.PNG)
Рисунок 2

![Снимок3](https://user-images.githubusercontent.com/114332610/198073408-e3b91832-f1ce-4300-a16d-52014bfb4025.PNG)
Рисунок 3

![Снимок4](https://user-images.githubusercontent.com/114332610/198073452-bc769a6d-71e6-4ba3-be01-c7c61219bfea.PNG)
Рисунок 4

-	Создайте на сцене плоскость, куб и сферу так, как показано на рисунке ниже. Создайте простой C# скрипт-файл и подключите его к сфере
-	В скрипт-файл RollerAgent.cs добавьте код, опубликованный в материалах лабораторных работ
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
-	Объекту «сфера» добавить компоненты Rigidbody, Decision Requester, Behavior Parameters
-	В корень проекта добавьте файл конфигурации нейронной сети.
-	Запустите работу ml-агента

![Снимок5](https://user-images.githubusercontent.com/114332610/198074029-5ce6117d-7c1c-4065-ab27-bffa7c8d5887.PNG)
Рисунок 5
-	Вернитесь в проект Unity, запустите сцену, проверьте работу ML-Agent’a.
-	![Снимок6](https://user-images.githubusercontent.com/114332610/198079514-6d3e88b4-a0ba-4eaa-9479-dd8e949410fc.PNG)
-	Сделайте 3, 9, 27 копий модели «Плоскость-Сфера-Куб», запустите симуляцию сцены и наблюдайте за результатом обучения модели.
-	![Снимок7](https://user-images.githubusercontent.com/114332610/198079566-9b86d1f3-2841-4277-ab0d-dea8a710811b.PNG)
-	После завершения обучения проверьте работу модели.
- Вывод: Проделав все манипуляции, описанные в методических указаниях к лабораторной работе а также в обучающем видеоматериале мне удалось протестировать работу MlAgent-а




## Задание 2
Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.
```
behaviors:
  RollerBall:
   trainer_type: ppo - Дефолтный тип обучения
    hyperparameters:
     batch_size: 10 - Количество опытов в каждой итерации градиентного спуска
      buffer_size: 100 - Количество опытов, которые необходимо собрать перед обновлением модели политики. Соответствует тому, сколько опыта должно быть собрано, прежде чем мы будем изучать или обновлять модель.
      learning_rate: 3.0e-4 - Определяет, как скорость обучения изменяется с течением времени.
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear - линейно снижает скорость обучения, достигая 0 на максимальных шагах
    network_settings:
      normalize: false - Применяется ли нормализация к входным данным векторного наблюдения.
      hidden_units: 128 - Количество блоков в скрытых слоях нейронной сети.
      num_layers: 2 - Количество скрытых слоев в нейронной сети.
    reward_signals:
      extrinsic: 
        gamma: 0.99 - Коэффициент дисконтирования для будущих вознаграждений, поступающих из окружающей среды
        strength: 1.0 - Фактор, на который умножается вознаграждение, данное средой.
    max_steps: 500000 - Общее количество шагов
    time_horizon: 64 - Сколько шагов опыта нужно собрать для каждого агента, прежде чем добавить его в буфер опыта.
    summary_freq: 10000 - Количество опытов, которое необходимо собрать перед созданием и отображением статистики обучения.
```
- The DecisionRequester автоматически запрашивает решения у агента через определённые промежутки времени.
- BehaviorParameters - компонент для настройки поведения агента и свойств его "мозга"
## Задание 3
Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости. 


## Выводы
- Игровой балланс - это в однопользовательских играх – вызов по времени.В многопользовательских – преимущество той или иной стороны.Где несколько стратегий – так ли одна стратегия лучше или хуже другой.При разнообразии объектов взаимодействия – соотношение выгоды/невыгоды между этими объектами.
-Системы машинного обучения могут широко использоваться для корректировки игрового балланса, например для сбора информации и подготовки тестовых баллансных правок, а также для поиска возможных уязвимостей и эксплойтов.


| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
