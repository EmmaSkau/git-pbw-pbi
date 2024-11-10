# git-pbw-pbi

Hej med jer! Vi skal lære jer om Git i dag!

import sqlite3

# Opret forbindelse til databasen
con = sqlite3.connect('cost_calculations.db')
print('Database åbnet')

try:
    # Slet eksisterende tabeller
    con.execute("DROP TABLE IF EXISTS Users;")
    con.execute("DROP TABLE IF EXISTS CostCalculations;")
    print('Tabeller slettet')
except Exception as e:
    print('Fejl ved sletning af tabel:', e)

try:
    # Opret Users tabel
    con.execute("""CREATE TABLE Users (
        user_id TEXT PRIMARY KEY,
        username TEXT NOT NULL,
        email TEXT UNIQUE NOT NULL
    );""")

    # Opret CostCalculations tabel
    con.execute("""CREATE TABLE CostCalculations (
        calculation_id INTEGER PRIMARY KEY AUTOINCREMENT,
        user_id TEXT,
        material_type TEXT NOT NULL,
        print_volume REAL NOT NULL,
        layer_thickness REAL NOT NULL,
        print_speed REAL NOT NULL,
        material_cost REAL NOT NULL,
        machine_time_cost REAL NOT NULL,
        energy_cost REAL NOT NULL,
        total_cost REAL NOT NULL,
        date TEXT DEFAULT (datetime('now', 'localtime')),
        FOREIGN KEY (user_id) REFERENCES Users(user_id)
    );""")
    print('Tabeller oprettet')
except Exception as e:
    print('Fejl ved oprettelse af tabeller:', e)

# Indsæt nogle eksempeldata
try:
    # Tilføj brugere
    con.execute("INSERT INTO Users (user_id, username, email) VALUES ('U001', 'Alice', 'alice@example.com');")
    con.execute("INSERT INTO Users (user_id, username, email) VALUES ('U002', 'Bob', 'bob@example.com');")
    print('Brugere tilføjet')

    # Tilføj omkostningsberegninger
    con.execute("""INSERT INTO CostCalculations (user_id, material_type, print_volume, layer_thickness, 
                    print_speed, material_cost, machine_time_cost, energy_cost, total_cost) 
                    VALUES ('U001', 'PLA', 100.5, 0.2, 50.0, 10.0, 15.0, 5.0, 30.0);""")
    con.execute("""INSERT INTO CostCalculations (user_id, material_type, print_volume, layer_thickness, 
                    print_speed, material_cost, machine_time_cost, energy_cost, total_cost) 
                    VALUES ('U002', 'ABS', 200.0, 0.3, 40.0, 20.0, 30.0, 10.0, 60.0);""")
    print('Omkostningsberegninger tilføjet')
except Exception as e:
    print('Fejl ved indsættelse af data:', e)

# Efter ændringer i databasen skal man kalde commit
con.commit()

# Kommandoer i programmet
inp = ''
print('\nKommandoer: ')
print(' vis - Vis data fra databasen')
print(' ny - Opret en ny bruger')
print(' q - Afslut program')

while not inp.startswith('q'):
    inp = input('> ')
    if inp == 'vis':
        c = con.cursor()
        # Vis Users tabellen
        c.execute('SELECT * FROM Users;')
        for p in c:
            print('Bruger:', p)
        # Vis CostCalculations tabellen
        c.execute('SELECT * FROM CostCalculations;')
        for p in c:
            print('Omkostningsberegning:', p)
    elif inp == 'ny':
        # Tilføj en ny bruger til Users tabellen
        user_id = input('Indtast bruger ID: ')
        username = input('Indtast brugernavn: ')
        email = input('Indtast email: ')
        c = con.cursor()
        try:
            c.execute('INSERT INTO Users (user_id, username, email) VALUES (?, ?, ?)', (user_id, username, email))
            con.commit()
            print('Ny bruger tilføjet')
        except Exception as e:
            print('Fejl ved indsættelse af ny bruger:', e)
    elif inp == 'q':
        print('Afslutter program...')
    else:
        print('Ugyldig kommando.')

# Luk forbindelsen til databasen
con.close()
