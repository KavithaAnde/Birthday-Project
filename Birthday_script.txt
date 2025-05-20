import sys
import csv 
import os 
from datetime import datetime 
employeelist = [] 
seen = set()
count = 0
valid = 0
invalid = 0
duplicate = 0
def getdata(data,umonth = "current"): 
    global employeelist 
    global count
    global duplicate
    global invalid    
    if umonth == "current":
        

        current_month = datetime.now().month 
    else:
        current_month = umonth
    for row in data: 
        count = count + 1

        try: 
            name = row["Name"].strip()
            birthday = row["Birthday"].strip() 
            if not name or not birthday:
                print("Skipping row with missing name or date.")
                continue
            if "/" in birthday:
                month,day,year = birthday.split("/")
            elif "-" in birthday:
                month,day,year = birthday.split("-") 
            else:
                print("Unknown date format:", birthday)
                continue
                invalid += 1
            if int(month) == current_month:         
                if (name,birthday) not in seen:
                    employeelist.append((name,birthday)) 


                    seen.add((name,birthday))
                else:
                    duplicate += 1
        except: 
            print("File Error, CSV file should contain Name and Birtday columns")  

def main():
    while True: 
        print("Enter the file name including the path")
        filename = input()
        if os.path.isfile(filename): 
            break 
        else:
            print(f"Invalid filename, make sure {filename} exists")
   
    with open(filename,"r") as file: 
        reader = csv.DictReader(file) 
        print("Enter (1) for employees whose birthday falls on current month or (2) for employees whose birthday based on custom month")
     
        choice = int(input())
        if choice == 1:
            getdata(reader) 
        elif choice == 2:
            print("Enter the month in mm format")
            month = int(input())
            getdata(reader, month)
        else:
            print("Invalid choice")

        if len(employeelist)==0: 
            print(f"No employees found")
        else:
            for index in range(len(employeelist)): 
                print(f"{employeelist[index][0]} : {employeelist[index][1]}") 
                  
        print("Total Rows :",count)
        print("Duplicates :",duplicate)
        print("Valid Data : ",(count - invalid))
        print("Invalid Data : ",invalid)

#Entry point
if __name__ == "__main__":

    main()
