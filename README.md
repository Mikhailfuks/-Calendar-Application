#include <iostream>
#include <vector>
#include <map>
#include <string>
#include <iomanip>
#include <ctime>

class Event {
public:
    Event(const std::string& title, const std::string& description)
        : title(title), description(description) {}

    void display() const {
        std::cout << "Title: " << title << "\nDescription: " << description << std::endl;
    }

private:
    std::string title;
    std::string description;
};

class Calendar {
public:
    void addEvent(const std::string& date, const std::string& title, const std::string& description) {
        events[date].emplace_back(title, description);
    }

    void showEvents(const std::string& date) const {
        auto it = events.find(date);
        if (it != events.end()) {
            std::cout << "Events on " << date << ":\n";
            for (const auto& event : it->second) {
                event.display();
                std::cout << "-----------------------" << std::endl;
            }
        } else {
            std::cout << "No events scheduled for " << date << "." << std::endl;
        }
    }

    void displayCurrentMonth() const {
        time_t now = time(0);
        tm* ltm = localtime(&now);
        
        int month = ltm->tm_mon + 1; // tm_mon is 0-indexed
        int year = ltm->tm_year + 1900;

        std::cout << "Current month: " << month << "/" << year << std::endl;
        std::cout << "Sun Mon Tue Wed Thu Fri Sat" << std::endl;

        // Set the first day of the current month
        tm firstDay = *ltm;
        firstDay.tm_mday = 1;
        firstDay.tm_mon = month - 1;
        mktime(&firstDay); // Normalize the firstDay structure

        // Print leading spaces
        int dayOfWeek = firstDay.tm_wday; 
        for (int i = 0; i < dayOfWeek; ++i)
            std::cout << "    ";

        // Get number of days in month
        int numDays = daysInMonth(month, year);
        
        // Print all days of the month
        for (int day = 1; day <= numDays; ++day) {
            std::cout << std::setw(3) << day << " ";
            if ((day + dayOfWeek) % 7 == 0)
                std::cout << std::endl; // Newline after Saturday
        }
        std::cout << std::endl;
    }

private:
    std::map<std::string, std::vector<Event>> events;

    int daysInMonth(int month, int year) const {
        if (month == 2) {
            // Check for leap year
            return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0) ? 29 : 28;
        }
        // Months with 30 days
        if (month == 4 || month == 6 || month == 9 || month == 11) {
            return
