# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
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
### Познакомиться с программными средствами для создания системы искусственного интеллекта и их применение совместно с Unity.

## Задание 1
### В проекте Unity реализовать перцептрон, который умеет призводить следующие вычисления:
- **OR**
- **AND**
- **NAND**
- **XOR**
- **Дать комментарии о корректности работы всех логических операций после реализации**

#### Подготовка проекта Unity
- Во первых создал новый 3D проект Unity. Добавил пустой объект на сцену.
- Затем дополнил файлы проекта C# скриптом, который реализует работу перцептрона:
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}
```
- "Привязал" скрипт к пустому объекту. Готовая сцена:

![image](https://user-images.githubusercontent.com/101344196/204860944-b5dcfd7a-667e-4dbb-9227-7758ed1cd00e.png)

#### Вычисление **OR**
- В Unity, а именно у пустого объекта заполнил лист **Ts**, который хранит значения для обучения перцептрона. Значения соответствовали математической операции **"ИЛИ"** для бинарного сложения (то есть: 0 или 0 = 0, 0 или 1 = 1 и так далее).

![image](https://user-images.githubusercontent.com/101344196/204869031-6cf77396-7dd8-40e2-be83-af19d3153d17.png)
- Проведя десятки тестов, пришёл к выводу, что 6 итераций обучения будет достаточно для успешной работы перцептрона с данной оперцией. На скриншоте пример работы обучения с 5-тью операциями. Задача перцептрона под конец обучения иметь **TOTAL ERROR: 0**. Как можно заметить, за пять итераций сушествует возможность ошибки в конце обучения. С шестью уже не наблюдалось. На скриншоте пример работы с пятью итерациями:  

![image](https://user-images.githubusercontent.com/101344196/204869543-696b5e30-9c4d-4fa1-a6a3-edad4f277ce3.png)
- В итоге 6 циклов обучения справились с задачей - данные обрабатываются по законам математической логики.

![image](https://user-images.githubusercontent.com/101344196/204870183-73f5b547-7ad8-45c3-8554-ced100aa6578.png)

#### Вычисление **AND**
- С математической операцией AND, перцептрону было достаточно 9 итераций обучения:

![image](https://user-images.githubusercontent.com/101344196/204880412-0855bb4f-f2e9-4f4b-b97c-c27dfc94d60a.png)

#### Вычисление **NAND**
- С NAND или NOT AND ситуация оказалось аналогична как и с AND - 9 шагов обучения:

![image](https://user-images.githubusercontent.com/101344196/204881524-d17d7b55-4e36-4177-8087-a4e975dfe859.png)

#### Вычисление **XOR**
- С XOR или "Исключающее «или»" было интереснее.
- Сколько бы нибыло тестов, программа не приходила к заветному нулю ошибок, наоборот после 2 итераций обучения достигала максимальных четырёх и не собиралась уменьшаться.
- Но объяснение этому уже дал Ма́рвин Ли Ми́нски - перцептрон расчитан только на линейно-связанные данные. XOR, как раз таки, нелинейна.
- И именно поэтому, перцептрон не смог сработать с данной логической операцией:

![image](https://user-images.githubusercontent.com/101344196/204882686-a731e304-4553-4262-81f1-c4522b3d7b91.png)

## Задание 2


## Задание 3


## Выводы
На практике разобрался как и зачем работает перцептрон. Выяснил, что он хорош в производстве вычислений именно для линейных данных. Получил дополнительный опыт работы в Unity. Ну и в итоге - реализовал перцептрон.
