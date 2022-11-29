# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Ямбушев Артём Дамирович
- РИ-210933
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Задание 3.
- Выводы.
- ✨Magic ✨

## Цель работы
# Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.
В данной лабораторной работе я создам ML-агент и буду тренировать нейросеть, задача которой будет заключаться в управлении шаром. Задача шара заключается в том, чтобы оставаясь на плоскости находить кубик, смещающийся в заданном случайном диапазоне координат.

## Задание 1
- С помощью Anaconda Prompt создал отдельную среду для машинного обучения, предварительно подключив python 3.6.13, mlagents 0.28.0 и pytorch 1.17.1.

![image](https://user-images.githubusercontent.com/101344196/200177971-2cb54d68-0358-4a74-97aa-1b6eafd2b517.png)

- Далее создал новый 3D проект Unity. Подключил пакеты ML Agents через встроенный пакетный менеджер.
- Создал на сцене плоскость, куб и сферу. Создал простой C# скрипт-файл и подключил его к сфере.
- В скрипт-файл добавил код для машинного обучения:
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
- Объекту «сфера» добавил компоненты Rigidbody, Decision Requester, Behavior Parameters и настроил их.
- Как выглядит сцена в Unity:
![image](https://user-images.githubusercontent.com/101344196/200179476-0ab45b77-f0c2-4a6d-b2b5-068594338248.png)
- В корень проекта добавил файл конфигурации нейронной сети.
- Успешно запустил работу ml-агента:
![image](https://user-images.githubusercontent.com/101344196/201158663-dde14ef2-ff78-412f-b460-5751f872106a.png)
- Я не стал мелочиться и сразу запустил обучение на 16-ти платформах. Выждал 9 итераций обучения.
![image](https://user-images.githubusercontent.com/101344196/201161688-915fa392-d22e-482b-8cd3-18b948842f95.png)
- С инструкциями из файла формата .onnx запустил проект Unity. Шар отлично выполнял свою работу по достижению куба.
![image](https://user-images.githubusercontent.com/101344196/201179353-ba43a649-5e16-4927-a881-5caae3a0b695.png)
## Задание 2


## Задание 3


## Выводы

# «Баланс — это долговечность. Если он быстро ломается — значит, баланса и не было»
Игровой баланс может корректироваться и строится на основе результатов машинного обучения. Машинное обучение может выявить ситуации в игре, которые не предусмотрел разработчик. Соответственно, будут предприняты меры по устранению, либо введению этих нестандартных ситуаций в игре посредством изменения кода.
