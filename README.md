from datetime import datetime

# Definieer het pad voor alle bestanden
appPath = r"C:\Test1\\"

print("= = = programma kentekensignalering gestart = = = ")

# Druk de huidige datum en tijd af
current_datetime = datetime.now()
current_date = current_datetime.date()
current_time = current_datetime.time()
print("Current Date:", current_date)
print("Current Time:", current_time)

# Open RDW.csv en GescandeData.csv
rdwInput = open(appPath + "RDW.csv", "r")
scanInput = open(appPath + "GescandeData.csv", "r")

aantalVerwerkt = aantalFout = 0

# Vraag of het bestand vals.txt leeggemaakt moet worden
legenVals = input("Wil je het bestand vals.txt leegmaken? (j of n): ")

if legenVals.lower() == "n":
    # Open bestand vals.txt om nieuwe gegevens toe te voegen
    signaleringen = open(appPath + "vals.txt", "a")
else:
    # Maak bestand vals.txt leeg
    signaleringen = open(appPath + "vals.txt", "w")

# Verwerk elke rij in GescandeData.csv
for auto in scanInput:
    if len(auto) < 10:
        continue

    autoDelen = auto.split(",")
    scanKenteken = autoDelen[0]
    scanMerk = autoDelen[1]
    scanType = autoDelen[2].replace("\n", "")
    aantalVerwerkt += 1
   
    match = 0
    rdwInput.seek(0)
    for rdwAuto in rdwInput:
        rdwAutoDelen = rdwAuto.split(",")
        rdwKenteken = rdwAutoDelen[0]
        rdwMerk = rdwAutoDelen[2]
        rdwType = rdwAutoDelen[3]

        # Als kentekens overeenkomen, dan wordt match op 1 gezet
        if scanKenteken == rdwKenteken:
            match = 1
            break

    if match == 0:
        # Onbekend kenteken
        aantalFout += 1
        print("Onbekend kenteken:", scanKenteken, scanMerk, scanType, "\n\n")
        signaleringen.write("Onbekend," + scanKenteken + "," + scanMerk + "," + scanType + "\n")
    else:
        if scanMerk != rdwMerk or scanType != rdwType:
            # Onjuist kenteken
            aantalFout += 1
            print("Onjuist kenteken:", scanKenteken, scanMerk, scanType)
            print("Geregistreerd op:", rdwKenteken, rdwMerk, rdwType, "\n\n")
            signaleringen.write("Onjuist," + scanKenteken + "," + scanMerk + "," + scanType + "\n")

# Print het aantal correcte en onjuiste kentekens
print("Aantal correcte kentekens:", aantalVerwerkt - aantalFout)
print("Aantal onjuiste kentekens:", aantalFout)

# Sluit alle geopende bestanden
rdwInput.close()
scanInput.close()
signaleringen.close()

print("Eindtotalen KentekenCheck")

print("= = =  programma kentekensignalering afgesloten = = =")

# Druk de huidige datum en tijd af
current_datetime = datetime.now()
current_date = current_datetime.date()
current_time = current_datetime.time()
print("Current Date:", current_date)
print("Current Time:", current_time)
