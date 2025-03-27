# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил:
- Лыжин Лев Дмитриевич
- РИ-230913
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | ? |
| Задание 2 | * | ? |
| Задание 3 | * | ? |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python. Также научиться анализировать игровые переменные, описывать их изменение и поведение

## Задание 1
### Выберите одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.
Ход работы:
Пункт "Здоровье" в играх играет ключевую роль в механике геймплея и балансировке сложности.
Этот пункт выполняет следующие функции:
- Определяет, сколько урона персонаж или враг может выдержать перед смертью
- Использовует ограниченное здоровье, чтобы повысить сложность
Здоровье появляется при возрождении в количестве 30 очков и за удар противника от этого числа отнимается 10. Здоровье можно пополнить вампиризмом или увеличить общий объем. 
- Схема экономической модели ресурса:
![image](https://github.com/user-attachments/assets/8870fc2d-32ce-40b2-86a8-3e9c60025023)


## Задание 2
### ### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализируйте данные в google-таблице (постройте график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Опишите характер изменения этой величины, опишите недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложите до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.
Ход работы:
- Изменение очков здоровья:
- При нанесении урона противником отнимается треть (10 xp) от общего количество здоровья. Общее количество здоровья или вампиризм можно увеличить при промощи выбора усилений.
-Визуализация изменения здоровья на примере графика:
![image](https://github.com/user-attachments/assets/b06eafc4-3d43-45da-a1dd-e9d63f0e30fc)

Ссылка на таблицу: https://docs.google.com/spreadsheets/d/1dJ7N0xdNUdQNl6_OsvEXfv9R4yB5kmQx6NBtpYE5uoc/edit?gid=0#gid=0
```Python
import gspread
from oauth2client.service_account import ServiceAccountCredentials

scope = ["https://spreadsheets.google.com/feeds", "https://www.googleapis.com/auth/drive"]
creds = ServiceAccountCredentials.from_json_keyfile_name("manifest-ocean-454918-u5-2e0d7133b1d4.json", scope)
client = gspread.authorize(creds)

spreadsheet = client.open("UnityWorkShop2")
sheet = spreadsheet.sheet1

# Начальные параметры
base_hp = 30
current_hp = base_hp
level = 0
hits_taken = 0
kills = 0
perks = 0  # Количество перков
vampirism = 0
next_level_threshold = 4  # Уровень дается за каждые 6 убийств

# Очистка таблицы и запись заголовков
sheet.clear()
sheet.update("A1:F1", [["Step", "Level", "HP", "Hits Taken", "Kills", "Perks"]])

# Игровой процесс на 19 шагов
for step in range(1, 20):

    # Каждые 3 шага — новый уровень + перки
    if kills >= next_level_threshold:
        level += 1
        perks += 1
        current_hp += 10
        if perks % 2 != 0: vampirism += 1
        next_level_threshold += 2  # Усложнение получения уровня

    #действия при шагах
    if step % 3 == 0:
        current_hp = current_hp - 10 + (vampirism * 3)
        hits_taken += 1
        kills += 2
        
        
    if current_hp == 0:
        sheet.append_row([step, level, current_hp, hits_taken, kills, perks])
        print(f"Шаг {step}: Уровень {level}, HP {current_hp}, Удары {hits_taken}, Убийства {kills}, Перки {perks}")
        break

    else:
        # HP не может быть меньше 0
        current_hp = max(current_hp, 0)
        # Запись данных в таблицу
        sheet.append_row([step, level, current_hp, hits_taken, kills, perks])
        print(f"Шаг {step}: Уровень {level}, HP {current_hp}, Удары {hits_taken}, Убийства {kills}, Перки {perks}")
```
Проблемы статы здоровья:
- Если посмотреть на график, то можно заметить сильный скачок относительно здоровья и получении урона, это говорит о том, что на начальных этапах игры здоровье плохо сбалансированно.

Как улучшить:
- Можно уменьшить поступаемый урон на начальных этапах игры и постепенно увеличивать его с достижением новых волн
- Увеличить начальный запас здоровья
- Установить максимум перку по увеличению общего количества здоровья, чтобы на поздних этапах игры становилось сложнее

## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.
Ход работы:
- Для реализации данного задания я выделил правильно по которым описывается динамика изменения здоровья:
    - Если игровой валюты хвататает на покупку, самого дорого оружия в магазине(coins = 10 000), то воспроизводится звук "хорошо"
    - Если игровой валюты хвататает на покупку, самого дешевого оружия в магазине(coins = 600), то воспроизводится звук "средне"
    - Если игровой валюты не хвататает на покупку, патронов в магазине (coins < 5), то воспроизводится звук "плохо"
- Для отображения этой динамики в Unity я создал компонент с таким скриптом (Это скрипт из методички, с небольшими изменениями):
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet.Count == 0) return ;

        if (dataSet["Mon_" + i.ToString()] == 10000  & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] == 600 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] < 5 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1zveTVn6nZdWTtuBqLlO_PmKmV9gEts89cSWNd-XlWFY/values/Лист1?key=AIzaSyAY025NahCQGwDFeFa7uJYgIBuRut70nVs");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```
- Как видно на изображении ниже, скрипт работает без проблем
![image](https://github.com/user-attachments/assets/dac14018-e79c-4f2d-85db-deee40536259)

## Выводы

В ходе работы я научился ананлизировать ресурсы определенной игре, визуализировать изменения и поведения этого ресурса, с помощью соотвествующих схем и ПО

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
