# Python Starting


Every language starts with “hello world”, as a DevOps, I’m going to start with environment~~

<!--more-->

* Windows 10 (nothing special)
* Chocolatery <https://chocolatey.org/install>
* Python

```Dos
choco install python -y
```

* Pycharm <https://www.jetbrains.com/pycharm>

Basic command:

```Python
# Hello World
print("Hello World!")
# Variable
# Calculation
a = 10
b = 50
c = a + b
# Print variable
name = "River"
print("%s is studying Python." % name)
# Condition
if True:
    print("True")
else:
    print("False")
i = 0
# for Loop
for i in range(10):
    print(i)
# while loop
while i < 10:
    print(i)
    i += 1
#for each loop
name_list = ['River', 'Bruno', 'Python']
for name in name_list:
    print(name)
```

Play with string in bit more advance:

```Python
name = input("Name: ").strip()
age = int(input("Age: "))
job = input("Job: ").strip()
msg = '''
Information of %s:
    Name: %s
    Age: %d
    Job: %s
''' % (name, name, age, job)
print(msg)
```

A basic login design:

* Login portal
  1. Asking for username and password
  2. Welcome message if authenticated successfully
  3. Lock the account if the authentication failed more than 3 times
* Multi-level menu
  1. 3 levels
  2. Select to sub-menu
  3. Use "List", "Dictionary"

```Python
# import module
import getpass,json,os,datetime
# detailed fucntions:
def new_locked_account(username):
    obj_file = open("locked.txt", 'a')
    obj_file.write("%s: %s" % (username, datetime.datetime.now()))
    obj_file.write("\n")
def get_locked_account(username):
    obj_file = open("locked.txt", 'r')
    read_file = obj_file.read()
    return (username in read_file)
def loginportal_menu():
    os.system("cls")
    print("Welcome to the login portal!")
    msg = '''Please select from following options:
    1: Start Login Portal
    0: Return to the main menu'''
    print(msg)
    for retry in range(3):
        option = input("Please select 1 or 0: ").strip()
        if option == '1':
            loginportal()
            break
        elif option == '0':
            main_menu()
            break
        else:
            print("Your input is %s, we are expecting either \"1\" or \"0\"" % option)
    else:
        print("Too many re-try, please restart the application!")
def loginportal():
    db_users = {
        'admin': 'admin',
        'River': 'python',
    }
    username = input("Please enter your username: ").strip()
    if get_locked_account(username):
        print("This account %s has been locked, please contact Administrator!" % username)
        exit()
    db_password = db_users.get(username)
    for retry in range(3):
        password = getpass.getpass(prompt='Please enter your password: ')
        if (db_password is not None) & (db_password == password):
            print("Authentication successful!")
            break
        else:
            print("Authentication Failed.")
    else:
        print("Lock username for %s due to too many re-try" % username)
        new_locked_account(username)
def multilevelmenu_menu():
    os.system("cls")
    print("Welcome to the multi-level menu demo!")
    msg = '''Please select from following options:
    1: Start Multi-level menu demo
    0: Return to the main menu'''
    print(msg)
    for retry in range(3):
        option = input("Please select 1 or 0: ").strip()
        if option == '1':
            multilevelmenu()
            break
        elif option == '0':
            main_menu()
            break
        else:
            print("Your input is %s, we are expecting either \"1\" or \"0\"" % option)
    else:
        print("Too many re-try, please restart the application!")
def member_list(menudata_team):
    teamdata = menudata_team['Members']
    for i in range(len(teamdata)):
        print("%s: %s" % (i + 1, teamdata[i]))
    print("0: Return to the previous menu")
    int_menuselect = None
    while (type(int_menuselect) is not int):
        menuselect = input("Please select 0 to return or other number keys to exit:")
        try:
            int_menuselect = int(menuselect)
        except ValueError:
            # handle if input cannot be converted to int
            int_menuselect = None
        if (type(int_menuselect) is int):
            break
        print("Your input is %s, we are expecting only above numbers." % menuselect)
    if int_menuselect == 0:
        multilevelmenu()
def team_list(menudata_group):
    options = {
        0: group_list,
    }
    groupdata = menudata_group['Teams']
    for i in range(len(groupdata)):
        print("%s: %s" % (i + 1, groupdata[i]['TeamName']))
        options[i + 1] = member_list
    print("0: Return to the previous menu")
    int_menuselect = None
    while (int_menuselect not in range(len(groupdata) + 1)) | (type(int_menuselect) is not int):
        menuselect = input("Please select 0 - %d:" % len(groupdata))
        try:
            int_menuselect = int(menuselect)
        except ValueError:
            # handle if input cannot be converted to int
            int_menuselect = None
        if (int_menuselect in range(len(groupdata) + 1)) & (type(int_menuselect) is int):
            break
        print("Your input is %s, we are expecting only above numbers." % menuselect)
    if int_menuselect == 0:
        multilevelmenu()
    else:
        options[int_menuselect](groupdata[int_menuselect - 1])
def group_list(menudata):
    options = {
        0: multilevelmenu_menu,
    }
    for i in range(len(menudata)):
        print("%s: %s" %(i+1,menudata[i]['GroupName']))
        options[i+1] = team_list
    print("0: Return to the main menu")
    int_menuselect = None
    while (int_menuselect not in range(len(menudata)+1)) | (type(int_menuselect) is not int):
        menuselect = input("Please select 0 - %d:" % len(menudata))
        try:
            int_menuselect = int(menuselect)
        except ValueError:
            # handle if input cannot be converted to int
            int_menuselect = None
        if (int_menuselect in range(len(menudata)+1)) & (type(int_menuselect) is int):
            break
        print("Your input is %s, we are expecting only above numbers." % menuselect)
    if int_menuselect == 0:
        options[int_menuselect]()
    else:
        options[int_menuselect](menudata[int_menuselect-1])
def multilevelmenu():
    obj_datafile = open("data.json", 'r')
    json_data = obj_datafile.read()
    #menudata as list
    menudata = json.loads(json_data)
    group_list(menudata)
def main_menu():
    options = {
        1: loginportal_menu,
        2: multilevelmenu_menu,
        0: exit,
    }
    os.system("cls")
    print("Welcome to the homework for Day1!")
    msg = '''Please select from following options:
    1: Login Portal
    2: Multi-level menu Demo
    0: Exit'''
    print(msg)
    for retry in range(3):
        option = input("Please select 1, 2 or 0: ").strip()
        if option not in ['0', '1', '2']:
            print("Your input is %s, we are expecting either \"1\", \"2\" or \"0\"" % option)
        else:
            option = int(option)
            options[option]()
            break
    else:
        print("Too many re-try, please restart the application!")
main_menu()
```

data.json:

```JSON
[
    {
        "GroupName": "Sales",
        "Teams":[
            {
                "TeamName": "Pre-Sales",
                "Members":[
                    "Terry",
                    "Tod",
                    "Lee"
                ]
            },
            {
                "TeamName": "Post-Sales",
                "Members":[
                    "Alex",
                    "Wang",
                    "Leo"
                ]
            },
            {
                "TeamName": "Direct-Sales",
                "Members":[
                    "Richard",
                    "Chris",
                    "Charlie"
                ]
            }
        ]
    },
    {
        "GroupName": "Ops",
        "Teams":[
            {
                "TeamName": "Network",
                "Members":[
                    "Sam",
                    "Phil",
                    "Tom"
                ]
            },
            {
                "TeamName": "Storage",
                "Members":[
                    "David",
                    "Echo",
                    "Frank"
                ]
            },
            {
                "TeamName": "Platform",
                "Members":[
                    "Lyn",
                    "Yang",
                    "Liu"
                ]
            }
        ]
    },
    {
        "GroupName": "Support",
        "Teams":[
            {
                "TeamName": "Enterprise Support",
                "Members":[
                    "Larden",
                    "Yuong",
                    "Tracy"
                ]
            },
            {
                "TeamName": "Public Support",
                "Members":[
                    "Pata",
                    "Nethan",
                    "Nir"
                ]
            },
            {
                "TeamName": "Internal Support",
                "Members":[
                    "Natalie",
                    "Mina",
                    "Merry"
                ]
            }
        ]
    }
]
```

Try more next time...

