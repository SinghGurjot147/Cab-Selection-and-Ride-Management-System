#include <iostream>
#include <iomanip>
#include <fstream>
#include <cstdlib>
#include <ctime>
using namespace std;

class Passenger {
public:
    string name, location;

    void inputDetails() {
        cout << "\nEnter your name: ";
        cin >> name;
        cout << "Enter your location: ";
        cin >> location;
    }
};

class Driver {
public:
    string name;
    float distance;
    float rating;
    float fare;

    void display() {
        cout << left << setw(12) << name 
             << " | Distance: " << setw(6) << distance 
             << "km | Rating: " << setw(4) << rating 
             << " | Fare: ₹" << fare << "\n";
    }
};

int main() {
    srand(time(0));

    fstream file;
    file.open("ride_history.txt", ios::app);

    if(!file) {
        cout << "Error opening file!";
        return 0;
    }

    while(true) {

        Passenger p;
        p.inputDetails();

        Driver drivers[5];

        // Generate drivers
        for(int i = 0; i < 5; i++) {
            drivers[i].name = "Driver_" + to_string(i + 1);
            drivers[i].distance = (rand() % 900 + 100) / 100.0; 
            drivers[i].rating = (rand() % 50 + 10) / 10.0;
            drivers[i].fare = (rand() % 200) + 50;
        }

        cout << "\nAvailable Drivers:\n";
        cout << "--------------------------------------------------------\n";
        for(int i = 0; i < 5; i++) drivers[i].display();

        // Strategy Selection Menu
        int choice;
        cout << "\nSelect Matching Method:\n";
        cout << "1. Nearest Driver (Least Distance)\n";
        cout << "2. Best Rated Driver (Highest Rating)\n";
        cout << "3. Lowest Fare Driver (Cheapest)\n";
        cout << "Enter your choice: ";
        cin >> choice;

        int bestIndex = 0;

        switch(choice) {
            case 1: // Nearest
                for(int i = 1; i < 5; i++)
                    if(drivers[i].distance < drivers[bestIndex].distance)
                        bestIndex = i;
                break;

            case 2: // Best Rating
                for(int i = 1; i < 5; i++)
                    if(drivers[i].rating > drivers[bestIndex].rating)
                        bestIndex = i;
                break;

            case 3: // Lowest Fare
                for(int i = 1; i < 5; i++)
                    if(drivers[i].fare < drivers[bestIndex].fare)
                        bestIndex = i;
                break;

            default:
                cout << "Invalid option. Selecting nearest driver by default.\n";
        }

        Driver selectedDriver = drivers[bestIndex];

        cout << "\nSelected Driver:\n--------------------------------------------------------\n";
        selectedDriver.display();

        // Save booking
        file << p.name << " from " << p.location << " was assigned to "
             << selectedDriver.name << " (Distance: " << selectedDriver.distance
             << " km, Rating: " << selectedDriver.rating 
             << ", Fare: ₹" << selectedDriver.fare << ")\n";

        cout << "\nRide Confirmed. Details saved successfully.\n";

        char again;
        cout << "\nBook another ride? (y/n): ";
        cin >> again;

        if(again == 'n' || again == 'N') break;
    }

    file.close();
    return 0;
}
