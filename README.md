# My_Own_Data_Analyst
This project is inspired by https://medium.com/@meliss85/why-you-need-to-be-your-own-data-analyst-finding-consistency-using-boxplot-f2a333bc3b27



This project is written in python using Kivy cross-platform framework. 

detailed description of the code is provided below:


firstly, required moduls need to be imported 

# MyAnalyst.py

## importing required libraries

### Kivy required libraries

```python
import kivy
from kivy.clock import Clock
from kivy.app import App
from kivy.uix.widget import Widget
from kivy.core.window import Window
from kivy.uix.textinput import TextInput
from kivy.lang import Builder
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.uix.boxlayout import BoxLayout

```
### Standard libraries
```python
import time
import os
import sqlite3
from datetime import datetime
```
### Data analysis and visualization libraries
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import math
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import math
```

## loading UI

The UI is written kv design language. you can load the .kv file using Builder.load_file(yourfile.kv) but in this project Builer.load_string() is prefered in order to prevent unexpected error or malfunction in the packaging stage.

```python
Builder.load_string("""<ManagerPage>:

    MainPage:

        name:"Main"

    AllGoalsPage:

        name:"AllGoals"

    TheGoal:

        name:"TheGoal"

    VisualizationAndAnalyzePage:

        name:"VisualizationAndAnalyze"


<MainPage>

    Label:
        id: date
        text: ''
        font_size : 26
        color : 0,0,0,0

    Button:
        id: go_to_my_goals_button
        text: "My Goals"
        size_hint : 0.25 , 0.2
        background_color : 0.8,0,1,1
        color: 0,1,1,1
        pos_hint:{'center_x': 0.85 , 'center_y':0.85}
        on_press:
            root.go_to_my_goals()

    Label :
        id : instruction
        text : ' Define a new goal or habit'
        font_size : 26
        color : 1,0,0,1
        pos_hint : { 'center_x' : 0.4 , 'center_y' : 0.9}


    Spinner:
        id : common_units
        text: "hours"
        pos_hint:{ 'center_x' : 0.4, 'center_y' : 0.5 }
        values : 'hours','minutes','quantity(#)',
        size_hint : 0.74 , 0.05
        on_text :
            root.define_goal(self.text)


    TextInput:
        id: goal_title
        multiline:False
        hint_text: "example: studying Data Analysis "
        size_hint : 0.74 , 0.05
        pos_hint:{'center_x': 0.4 , 'center_y':0.7}


    TextInput:
        id : min_variable
        multiline: False
        hint_text : 'minimum desired example : 3'
        size_hint : 0.74 , 0.05
        pos_hint : {'center_x' : 0.4 , 'center_y':0.6}


    TextInput:
        id : max_variable
        hint_text: 'maximum desired example : 7'
        size_hint : 0.74 , 0.05
        pos_hint : {'center_x' : 0.4 , 'center_y': 0.4}
        multiline: False




    TextInput:
        id : other_variable
        multiline : False
        size_hint : 0.0 , 0.0
        pos_hint : {'center_x':0, 'center_y':0}


    Button:
        id: define_goal
        text: "ok"
        pos_hint:{'center_x':0.4 , 'center_y': 0.3}
        size_hint: 0.74 , 0.05
        background_color: 0.8,0,1,1
        color: 0,1,1,1
        on_press:
            root.define_goal()





<AllGoalsPage>:

    Button:
        id: back_button
        text: "<<<"
        font_size: 40
        color: 1,0,0,1
        background_color : 0,0,0,0
        pos_hint :{'center_x' : 0.15 , 'center_y': 0.95}
        on_press: root.manager.current = "Main"

    Label:
        id: initial_txt
        text: "You have not defined any habit"
        color: 0,0,0,0
        pos_hint:{'center_x': 0.5 , 'center_y':0.5}
    Button:
        id: goal1
        text: ''
        color: 0,1,1,1
        size_hint: 0.42, 0.1
        pos_hint:{'center_x': 0.25 , 'center_y':0.85}
        background_color: 0.8,0,1,1
        disabled: True
        on_press: root.check_the_goal(root.ids.goal1.text)

    Button:
        id: goal2
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        on_press: root.check_the_goal(root.ids.goal2.text)
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.25 , 'center_y':0.7}

    Button:
        id: goal3
        color: 0,0,0,0
        text: ''
        size_hint: 0.42, 0.1
        on_press: root.check_the_goal(root.ids.goal3.text)
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.25 , 'center_y':0.55}

    Button:
        id: goal4
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        on_press: root.check_the_goal(root.ids.goal4.text)
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.25 , 'center_y':0.4}

    Button:
        id: goal5
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        on_press: root.check_the_goal(root.ids.goal5.text)
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.25 , 'center_y':0.25}


    Button:
        id: goal6
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        background_color: 0,0,0,0
        disabled: True
        on_press: root.check_the_goal(root.ids.goal6.text)
        pos_hint:{'center_x': 0.25 , 'center_y':0.1}

    Button:
        id: goal7
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        background_color: 0,0,0,0
        disabled: True
        on_press: root.check_the_goal(root.ids.goal7.text)
        pos_hint:{'center_x': 0.75 , 'center_y':0.85}

    Button:
        id: goal8
        text: ''
        color: 0,0,0,0
        background_color: 0,0,0,0
        disabled: True
        size_hint: 0.42, 0.1
        on_press: root.check_the_goal(root.ids.goal8.text)
        pos_hint:{'center_x': 0.75 , 'center_y':0.7}

    Button:
        id: goal9
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.1
        background_color: 0,0,0,0
        disabled: True
        on_press: root.check_the_goal(root.ids.goal9.text)
        pos_hint:{'center_x': 0.75 , 'center_y':0.55}

    Button:
        id: goal10
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.15
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.75 , 'center_y':0.4}
        on_press: root.check_the_goal(root.ids.goal10.text)


    Button:
        id: goal11
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.15
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.75 , 'center_y':0.25}
        on_press: root.check_the_goal(root.ids.goal10.text)

    Button:
        id: goal12
        text: ''
        color: 0,0,0,0
        size_hint: 0.42, 0.15
        background_color: 0,0,0,0
        disabled: True
        pos_hint:{'center_x': 0.75 , 'center_y':0.10}
        on_press: root.check_the_goal(root.ids.goal10.text)










<TheGoal>:

    Label:
        id: goal_description
        text: ""
        color : 1,0,0,1
        pos_hint:{'center_x': 0.3 , 'center_y':0.87}

    Label:
        id: date2
        text:''
        color : 1,0,0,1
        pos_hint:{'center_x': 0.3 , 'center_y':0.78}

    Label:
        id: performance_question
        text: "how did you do today?"
        color : 1,0,0,1
        pos_hint:{'center_x': 0.3 , 'center_y':0.6}

    TextInput
        id: performance_value
        multiline: False
        pos_hint:{ 'center_x': 0.7 , 'center_y':0.6}
        size_hint : 0.25 , 0.05


    Button:
        id: submit
        pos_hint:{'center_x' : 0.7 , 'center_y': 0.5}
        size_hint: 0.15 , 0.1
        text: "submit"
        on_press: root.submit_record()

    Button:
        id: next_button
        pos_hint:{'center_x' : 0.7 , 'center_y': 0.5}
        size_hint: 0.15 , 0.1
        text: "Next"
        on_press: root.manager.current = "VisualizationAndAnalyze"



<VisualizationAndAnalyzePage>:

    Label:
        id: date_3
        text: ""
        color : 1,0,0,1
        pos_hint : {'center_x': 0.2 , 'center_y':0.87}

    Label:
        id: goal_description2
        text:''
        color : 1,0,0,1
        pos_hint:{'center_x': 0.5 ,'center_y': 0.87}



    Label:
        id: txt_1
        text: ""
        pos_hint: {'center_x': 0.5 , 'center_y': 0.75}

    Label:
        id: txt_2
        text: "Keep your performance inside the box"
        pos_hint: {'center_x': 0.5 , 'center_y': 0.7}

    Label:
        id: txt_3
        text: "if you observe points inside the box range ...well done !!"
        pos_hint: {'center_x': 0.5 , 'center_y': 0.65}

    Label:
        id: txt_4
        text: "if you observe any point above the max you have exceeded your expectation ... good job!"
        pos_hint: {'center_x': 0.6 , 'center_y': 0.6}

    Label:
        id: txt_5
        text: "if you observe any point under the minimum you need to improve. try to cath up "
        pos_hint: {'center_x': 0.6 , 'center_y': 0.55}

    Label:
        id: txt_6
        text: ""
        pos_hint: {'center_x': 0.6 , 'center_y': 0.4}

    Button:
        id: show_performance
        pos_hint:{'center_x' : 0.2 , 'center_y': 0.25}
        size_hint: 0.3 , 0.15
        text: "show"
        on_press: root.update_and_visualize_consistency()


    Image:
        id: performance_visualization
        pos_hint:{'center_x': 0.7 , 'center_y': 0.25}
        size_hint: 0.6 , 0.6
        source:""")
```



in this part , the first and main screen is created where we define goals 

The method arrange_all_goals() gets the number of tables(each table in dtabase represents a goal) and assigns a button to each on the "AllGolas" screen

```python

class MainPage(Screen):

    def arrange_all_goals(self):

        conn = sqlite3.connect('test6.db')

        cur = conn.cursor()

        cur.execute("SELECT * FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%';")
        conn.commit()
        goals = cur.fetchall()

        for g in range(len(goals)):

            if g == 0:
                self.manager.get_screen("AllGoals").ids.goal1.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal1.color = 0,1,1,1
                self.manager.get_screen("AllGoals").ids.goal1.background_color = 0.8,0,1,1,
                self.manager.get_screen("AllGoals").ids.goal1.disabled = False


            if g == 1:
                self.manager.get_screen("AllGoals").ids.goal2.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal2.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal2.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal2.disabled = False

            if g == 2:
                self.manager.get_screen("AllGoals").ids.goal3.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal3.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal3.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal3.disabled = False

            if g == 3:
                self.manager.get_screen("AllGoals").ids.goal4.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal4.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal4.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal4.disabled = False

            if g == 4:
                self.manager.get_screen("AllGoals").ids.goal5.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal5.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal5.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal5.disabled = False

            if g == 5:
                self.manager.get_screen("AllGoals").ids.goal6.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal6.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal6.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal6.disabled = False

            if g == 6:
                self.manager.get_screen("AllGoals").ids.goal7.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal7.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal7.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal7.disabled = False

            if g == 7:
                self.manager.get_screen("AllGoals").ids.goal8.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal8.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal8.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal8.disabled = False

            if g == 8:
                self.manager.get_screen("AllGoals").ids.goal9.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal9.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal9.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal9.disabled = False

            if g == 9:
                self.manager.get_screen("AllGoals").ids.goal10.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal10.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal10.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal10.disabled = False

            if g == 10:
                self.manager.get_screen("AllGoals").ids.goal11.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal11.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal11.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal11.disabled = False

            if g == 11:
                self.manager.get_screen("AllGoals").ids.goal12.text = goals[g][1]
                self.manager.get_screen("AllGoals").ids.goal12.color = 0, 1, 1, 1
                self.manager.get_screen("AllGoals").ids.goal12.background_color = 0.8, 0, 1, 1,
                self.manager.get_screen("AllGoals").ids.goal12.disabled = False

            self.manager.current = "AllGoals"

```

the method go_to_my_goals() bring us to the AllGoals screen. if no goals defined the label "You have not defined any habit" will be displayed otherwise it calls the arrange_all_goals() and displays buttons corresponding goals.

```python

    def go_to_my_goals(self):

        conn = sqlite3.connect("test6.db")

        cur = conn.cursor()

        cur.execute("SELECT * FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%';")

        goals = cur.fetchall()

        conn.commit()

        if len(goals) == 0:

            self.manager.get_screen("AllGoals").ids.initial_txt.color = 1, 0, 0, 1

            self.manager.current = "AllGoals"

        else:
            self.arrange_all_goals()
```

the define_goal() below is called when user hits the ok button after entering inputs of goal

```python
    def define_goal(self):

        minimum = self.ids.min_variable.text

        maximum = self.ids.max_variable.text

        # creating database

        conn = sqlite3.connect('test6.db')

        cur = conn.cursor()

        # crating table

        table_name = self.ids.goal_title.text

        table_name = table_name.replace(" ", "_")

        create_table_query = "CREATE TABLE IF NOT EXISTS {} (reference_date TEXT,date TEXT ,week REAL,performance REAL, minimum REAL , maximum REAL)".format(
            table_name)

        cur.execute(create_table_query)

        conn.commit()

        goal_defined_date = datetime.today().strftime('%d-%m-%Y')

        cur.execute("INSERT INTO {} (reference_date,minimum,maximum) VALUES (?,?, ?)".format(table_name),
                    (goal_defined_date, minimum, maximum))

        conn.commit()

        conn.close()

        self.manager.get_screen("AllGoals").ids.goal1.text = table_name

        self.arrange_all_goals()
```


### AllGoals Screen

we define a check_the_goal( table) associated with each button which accepts table parameter 

```python

class AllGoalsPage(Screen):

    def check_the_goal(self, table):
        self.manager.get_screen("TheGoal").ids.date2.text = datetime.today().strftime('%d-%m-%Y')

        self.manager.get_screen("TheGoal").ids.goal_description.text = table

        self.manager.get_screen("VisualizationAndAnalyze").ids.date_3.text = datetime.today().strftime('%d-%m-%Y')

        self.manager.get_screen("VisualizationAndAnalyze").ids.goal_description2.text = table

        self.manager.current = "TheGoal"
```


### TheGoal Screen

in this screen an input regarding users daily performance is taken and inserted to the database 


```python

class TheGoal(Screen):

    def submit_record(self):

        conn = sqlite3.connect('test6.db')

        cur = conn.cursor()

        cur.execute("SELECT reference_date FROM {} ".format(self.ids.goal_description.text))

        reference_date_list = cur.fetchall()

        conn.commit()

        reference_date = datetime.strptime(reference_date_list[0][0], "%d-%m-%Y")

        current_date_str = datetime.today().strftime('%d-%m-%Y')

        current_date = datetime.strptime(current_date_str, '%d-%m-%Y')

        delta_days = (current_date - reference_date).days

        week_number = math.ceil(delta_days / 7)

        performance_value = self.ids.performance_value.text

        cur.execute("SELECT minimum , maximum  FROM {} ".format(self.ids.goal_description.text))

        repetitive_min_max = cur.fetchall()

        #print(repetitive_min_max)




        conn.commit()

        table_name = self.ids.goal_description.text

        cur.execute("SELECT date FROM {} ".format(self.ids.goal_description.text))

        dates_list = cur.fetchall()

        conn.commit()

        if dates_list[0][0] is None:

            cur.execute("INSERT INTO {} (date,week,performance) VALUES (?,?,?)".format(table_name), (

                current_date_str, week_number, performance_value))

            conn.commit()

        else:

            cur.execute("INSERT INTO {} (date,week,performance,minimum,maximum) VALUES (?,?,?,?, ?)".format(table_name),
                        (

                            current_date_str, week_number, performance_value, repetitive_min_max[0][0],
                            repetitive_min_max[0][1]))

            conn.commit()

            conn.close()

```


### Visualization screen 

in this screen useres' data are visualized and plotted using scattered boxplot 


```python

class VisualizationAndAnalyzePage(Screen):

    def update_and_visualize_consistency(self):
        conn = sqlite3.connect('test6.db')

        cur = conn.cursor()

        cur.execute(
            "SELECT {} , {} , {}  FROM {} ".format('date', 'week', 'performance', str(self.ids.goal_description2.text)))

        original_data = cur.fetchall()

        conn.commit()

        original_df = pd.DataFrame(original_data)

        cur.execute("SELECT {} , {}  FROM {} ".format('minimum', 'maximum', self.ids.goal_description2.text))

        min_max = cur.fetchone()

        print(min_max)

        conn.commit()

        original_df.columns = ['date', 'week', 'performance']

        fig, ax = plt.subplots()

        original_df['date'] = pd.to_datetime(original_df['date'], infer_datetime_format=True)

        original_df['date'] = original_df['date'].astype(str)

        temp = list(original_df['week'])

        my_labels = list(set(temp))

        print(my_labels)

        plt.boxplot(x=min_max)

        plt.scatter(x=original_df['week'], y=original_df['performance'])

        image_path = "visual_image.png"

        plt.savefig(image_path)

        self.ids.performance_visualization.source = image_path

        self.ids.txt_1.text = "minimum desired value :{}  maximum : {}".format(min_max[0],min_max[1])

        self.ids.txt_1.color = 0,0,0,1

        self.ids.txt_2.color = 0,0,0,1

        self.ids.txt_3.color = 0,0,0,1

        self.ids.txt_4.color = 0,0,0,1

        self.ids.txt_5.color = 0, 0, 0, 1

```



and here are the rest of our kivy code 

```python

class ManagerPage(ScreenManager):
    pass


class My_Analyst(App):

    def build(self):
        return ManagerPage()


if __name__ == '__main__':
    My_Analyst().run()
```
