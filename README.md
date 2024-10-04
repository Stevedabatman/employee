#include <iostream>
#include <string>
#include <iomanip>
#include <sstream> // for std::fixed and std::setprecision
using namespace std;

const int MAX_EMPLOYEES = 100;

class Employee {
private:
    string employeeID;
    string name;
    string employeeType;

public:
    Employee() {}

    void inputDetails() {
        cout << "Enter Employee ID: ";
        cin >> employeeID;
        cout << "Enter Employee Name: ";
        cin.ignore();
        getline(cin, name);
        cout << "Enter Employee Type (Full-time/Part-time): ";
        getline(cin, employeeType);
    }

    string getEmployeeID() {
        return employeeID;
    }

    string getEmployeeType() {
        return employeeType;
    }

    string getName() {
        return name;
    }

    void displayDetails() {
        cout << "Employee ID: " << employeeID << endl;
        cout << "Employee Name: " << name << endl;
        cout << "Employee Type: " << employeeType << endl;
    }
};

class Salary : public Employee {
private:
    double basicSalary;
    double hourlyWage;
    int leaveDays;
    int overtimeHours;
    int hoursWorked;

public:
    Salary() : basicSalary(0), hourlyWage(0), leaveDays(0), overtimeHours(0), hoursWorked(0) {}

    void choosePosition() {
        int choice;
        cout << "Select Position:\n";
        cout << "1. Barista ($1920)\n";
        cout << "2. Cashier ($1790)\n";
        cout << "3. Kitchen Staff (Part-time) ($7.7/hour)\n";
        cout << "4. Cleaner (Part-time) ($5.8/hour)\n";
        cout << "5. Waiter/Waitress (Part-time) ($7/hour)\n";
        cout << "6. Supervisor ($2400)\n";
        cout << "Enter the number corresponding to the position: ";
        cin >> choice;

        switch (choice) {
            case 1:
                basicSalary = 1920;
                hourlyWage = 8;
                cout << "Position: Barista\n";
                break;
            case 2:
                basicSalary = 1790;
                hourlyWage = 7.5;
                cout << "Position: Cashier\n";
                break;
            case 3:
                hourlyWage = 7.7;
                cout << "Position: Kitchen Staff (Part-time)\n";
                break;
            case 4:
                hourlyWage = 5.8;
                cout << "Position: Cleaner (Part-time)\n";
                break;
            case 5:
                hourlyWage = 7;
                cout << "Position: Waiter/Waitress (Part-time)\n";
                break;
            case 6:
                basicSalary = 2400;
                hourlyWage = 10;
                cout << "Position: Supervisor\n";
                break;
            default:
                cout << "Invalid choice, setting default as Barista.\n";
                basicSalary = 1920;
                hourlyWage = 8;
        }
    }

    void inputWorkDetails() {
        if (getEmployeeType() == "Part-time") {
            cout << "Enter hours worked: ";
            cin >> hoursWorked;
        } else {
            cout << "Enter Overtime Hours: ";
            cin >> overtimeHours;
            cout << "Enter Leave Days: ";
            cin >> leaveDays;
        }
    }

    double calculatePartTimeSalary() {
        return hoursWorked * hourlyWage;
    }

    double calculateLeaveBonus() {
        if (leaveDays > 2) {
            return 0;
        }
        switch (leaveDays) {
            case 0:
                return 500;
            case 1:
                return 200;
            case 2:
                return 100;
            default:
                return 0;
        }
    }

    double calculateFullTimeNetSalary() {
        double dailyWage = basicSalary / 30;
        double overtimePay = overtimeHours * (hourlyWage * 2);
        double leaveDeduction = 0;

        if (leaveDays > 4) {
            leaveDeduction = (basicSalary * 0.01);
        }

        double grossSalary = basicSalary + overtimePay + calculateLeaveBonus() - leaveDeduction;
        double tax = grossSalary * 0.02;
        double totalDeductions = tax + 10;

        double netSalary = grossSalary - totalDeductions;
        return netSalary;
    }

    double getNetSalary() {
        if (getEmployeeType() == "Part-time") {
            return calculatePartTimeSalary();
        } else {
            return calculateFullTimeNetSalary();
        }
    }

    double calculateFullTimeBasicSalary() {
        return basicSalary; // Return the basic salary for full-time employees
    }

    void displaySalaryDetails() {
        if (getEmployeeType() == "Part-time") {
            cout << "Hourly Wage: $" << hourlyWage << endl;
            cout << "Hours Worked: " << hoursWorked << endl;
            cout << "Total Salary: $" << calculatePartTimeSalary() << endl;
        } else {
            cout << "Basic Salary: $" << basicSalary << endl;
            cout << "Hourly Wage: $" << hourlyWage << endl;
            cout << "Overtime Hours: " << overtimeHours << endl;
            cout << "Leave Days: " << leaveDays << endl;
            cout << "Leave Bonus: $" << calculateLeaveBonus() << endl;
            cout << "Net Salary: $" << calculateFullTimeNetSalary() << endl;
        }
    }
};

Salary employees[MAX_EMPLOYEES];
int employeeCount = 0;

void merge(Salary arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    Salary* L = new Salary[n1];
    Salary* R = new Salary[n2];

    for (int i = 0; i < n1; i++)
        L[i] = arr[left + i];
    for (int j = 0; j < n2; j++)
        R[j] = arr[mid + 1 + j];

    int i = 0, j = 0, k = left;

    while (i < n1 && j < n2) {
        if (L[i].getEmployeeID() <= R[j].getEmployeeID()) {
            arr[k] = L[i];
            i++;
        } else {
            arr[k] = R[j];
            j++;
        }
        k++;
    }

    while (i < n1) {
        arr[k] = L[i];
        i++;
        k++;
    }

    while (j < n2) {
        arr[k] = R[j];
        j++;
        k++;
    }

    delete[] L;
    delete[] R;
}

void mergeSort(Salary arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;

        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

void showMenu() {
    cout << "----- Employee Management System -----" << endl;
    cout << "1. Insert Employee Data" << endl;
    cout << "2. Update Employee Data" << endl;
    cout << "3. View Employee Data" << endl;
    cout << "4. Exit" << endl;
    cout << "--------------------------------------" << endl;
    cout << "Choose an option: ";
}

void displayReports(Salary employees[], int employeeCount) {
    double totalNetSalary = 0;

    // Employee Overview
    cout << "\n--- Employee Overview ---" << endl;
    cout << left << setw(12) << "ID" 
         << setw(20) << "Name" 
         << setw(16) << "Type" 
         << setw(20) << "Basic Salary" 
         << setw(16) << "Net Salary" << endl;
    cout << "--------------------------------------------------------------------------" << endl;

    for (int i = 0; i < employeeCount; i++) {
        cout << left << setw(12) << employees[i].getEmployeeID()
             << setw(20) << employees[i].getName()
             << setw(16) << employees[i].getEmployeeType()
             << setw(20) << (employees[i].getEmployeeType() == "Full-time" 
                             ? "$" + (std::ostringstream() << employees[i].calculateFullTimeBasicSalary()).str() 
                             : "$" + (std::ostringstream() << employees[i].calculatePartTimeSalary()).str()) // Updated this line
             << "$" << fixed << setprecision(2) << setw(10) << employees[i].getNetSalary() << endl;

        totalNetSalary += employees[i].getNetSalary();
    }

    // Full-Time Employees
    cout << "\n--- Full-Time Employees ---" << endl;
    cout << left << setw(12) << "ID" 
         << setw(20) << "Name" 
         << setw(20) << "Basic Salary" 
         << setw(16) << "Net Salary" << endl;
    cout << "--------------------------------------------------------------------------" << endl;

    for (int i = 0; i < employeeCount; i++) {
        if (employees[i].getEmployeeType() == "Full-time") {
            cout << left << setw(12) << employees[i].getEmployeeID()
                 << setw(20) << employees[i].getName()
                 << setw(20) << "$" + to_string(employees[i].calculateFullTimeBasicSalary())
                 << "$" << fixed << setprecision(2) << setw(10) << employees[i].getNetSalary() << endl;
        }
    }

    // Part-Time Employees
    cout << "\n--- Part-Time Employees ---" << endl;
    cout << left << setw(12) << "ID" 
         << setw(20) << "Name" 
         << setw(20) << "Basic Salary" 
         << setw(16) << "Total Salary" << endl;
    cout << "--------------------------------------------------------------------------" << endl;

    for (int i = 0; i < employeeCount; i++) {
        if (employees[i].getEmployeeType() == "Part-time") {
            cout << left << setw(12) << employees[i].getEmployeeID()
                 << setw(20) << employees[i].getName()
                 << setw(20) << "$" + to_string(employees[i].calculatePartTimeSalary())
                 << "$" << fixed << setprecision(2) << setw(10) << employees[i].calculatePartTimeSalary() << endl;
        }
    }

    // Salary Report
    cout << "\n--- Salary Report ---" << endl;
    cout << left << setw(12) << "ID" 
         << setw(20) << "Name" 
         << setw(16) << "Type" 
         << setw(16) << "Net Salary" << endl;
    cout << "--------------------------------------------------------------------------" << endl;

    for (int i = 0; i < employeeCount; i++) {
        cout << left << setw(12) << employees[i].getEmployeeID()
             << setw(20) << employees[i].getName()
             << setw(16) << employees[i].getEmployeeType()
             << "$" << fixed << setprecision(2) << setw(10) << employees[i].getNetSalary() << endl;
    }
    
    cout << "--------------------------------------------------------------------------" << endl;
    cout << "Total Net Salary: $" << fixed << setprecision(2) << totalNetSalary << endl;
}

int main() {
    int choice;
    do {
        showMenu();
        cin >> choice;
        switch (choice) {
            case 1:
                if (employeeCount < MAX_EMPLOYEES) {
                    employees[employeeCount].inputDetails();
                    employees[employeeCount].choosePosition();
                    employees[employeeCount].inputWorkDetails();
                    employeeCount++;
                } else {
                    cout << "Employee limit reached!" << endl;
                }
                break;
            case 2:
                cout << "Update functionality not implemented yet." << endl;
                break;
            case 3:
                displayReports(employees, employeeCount); // Pass parameters here
                break;
            case 4:
                cout << "Exiting system. Goodbye!" << endl;
                break;
            default:
                cout << "Invalid choice. Try again." << endl;
        }
    } while (choice != 4);

    return 0;
}
