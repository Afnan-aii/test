#include<iostream>
#include<string>
#include<vector>
using namespace std;

class Seat {
private:
    string label, type;
    bool booked;
    int price;

public:
    Seat(string l, string t, int p)
        : label(l), type(t), booked(false), price(p) {}


    string getLabel() { return label; }
    string getType() { return type; }
    bool isBooked() { return booked; }
    int getPrice() { return price; }


    void setBooked(bool b) { booked = b; }
};

class Movie {
private:
    int id;
    string title, genre, time;

public:
    Movie(int i, string t, string g, string tm)
        : id(i), title(t), genre(g), time(tm) {}


    int getId() { return id; }
    string getTitle() { return title; }
    string getGenre() { return genre; }
    string getTime() { return time; }
};

class Booking {
private:
    int id, price;
    string customer, movie, seat, type;

public:

    void setId(int i) { id = i; }
    void setPrice(int p) { price = p; }
    void setCustomer(string c) { customer = c; }
    void setMovie(string m) { movie = m; }
    void setSeat(string s) { seat = s; }
    void setType(string t) { type = t; }


    int getId() { return id; }
    int getPrice() { return price; }
    string getCustomer() { return customer; }
    string getMovie() { return movie; }
    string getSeat() { return seat; }
    string getType() { return type; }
};

vector<Movie> movies;
vector<Seat> seats;
vector<Booking> bookings;

int bookingCount = 0;

void init() {

    movies.push_back(Movie(1, "Interstellar", "Sci-Fi", "10:00 AM"));
    movies.push_back(Movie(2, "The Dark Knight", "Action", "01:00 PM"));
    movies.push_back(Movie(3, "Inception", "Thriller", "04:00 PM"));

    string rows = "ABCD";

    for(int r = 0; r < 4; r++) {

        for(int c = 1; c <= 5; c++) {

            string lbl = "";
            lbl += rows[r];
            lbl += (char)('0' + c);

            if(r == 0)
                seats.push_back(Seat(lbl, "VIP", 1500));

            else if(r == 1)
                seats.push_back(Seat(lbl, "Premium", 1000));

            else
                seats.push_back(Seat(lbl, "Standard", 750));
        }
    }
}

void showMovies() {

    cout << "\n========= NOW SHOWING =========\n";

    for(int i = 0; i < (int)movies.size(); i++) {

        cout << " [" << movies[i].getId() << "] "
             << movies[i].getTitle()
             << " | " << movies[i].getGenre()
             << " | " << movies[i].getTime() << "\n";
    }

    cout << "================================\n";
}

void showSeats() {

    cout << "\n--- SEAT MAP ---\n";
    cout << " VIP=1500Rs  Premium=1000Rs  Standard=750Rs\n\n";

    for(int i = 0; i < (int)seats.size(); i++) {

        if(seats[i].isBooked())
            cout << "[XX] ";

        else
            cout << "[" << seats[i].getLabel() << "] ";

        if((i + 1) % 5 == 0)
            cout << "\n";
    }

    cout << "\n";
}

void bookTicket(int mid = -1) {

    showMovies();

    if(mid == -1) {

        cout << " Enter Movie ID: ";
        cin >> mid;
    }

    string mname = "";

    for(int i = 0; i < (int)movies.size(); i++) {

        if(movies[i].getId() == mid)
            mname = movies[i].getTitle();
    }

    if(mname == "") {

        cout << " Invalid movie!\n";
        return;
    }

    cout << " Selected: " << mname << "\n";

    showSeats();

    string sl;

    cout << " Enter Seat Label (e.g. A1, B3): ";
    cin >> sl;

    int idx = -1;

    for(int i = 0; i < (int)seats.size(); i++) {

        if(seats[i].getLabel() == sl)
            idx = i;
    }

    if(idx == -1) {

        cout << " Seat not found!\n";
        return;
    }

    if(seats[idx].isBooked()) {

        cout << " Seat already booked!\n";
        return;
    }

    string cname;

    cout << " Enter your name: ";
    cin >> cname;

    seats[idx].setBooked(true);

    Booking b;

    b.setId(++bookingCount);
    b.setCustomer(cname);
    b.setMovie(mname);
    b.setSeat(sl);
    b.setPrice(seats[idx].getPrice());
    b.setType(seats[idx].getType());

    bookings.push_back(b);

    cout << "\n******* BOOKING CONFIRMED *******\n";

    cout << " Booking ID : #" << b.getId() << "\n";
    cout << " Customer   : " << b.getCustomer() << "\n";
    cout << " Movie      : " << b.getMovie() << "\n";
    cout << " Seat       : " << b.getSeat()
         << " (" << b.getType() << ")\n";

    cout << " Amount     : Rs." << b.getPrice() << "\n";

    cout << "*********************************\n";
}

void viewMoviesMenu() {

    showMovies();

    cout << " Enter Movie ID to Book, or 0 to go Back: ";

    int ch;
    cin >> ch;

    if(ch == 0)
        return;

    bookTicket(ch);
}

void viewSeatsMenu() {

    showSeats();

    cout << " Press 1 to Book a Seat, or 0 to go Back: ";

    int ch;
    cin >> ch;

    if(ch == 1)
        bookTicket();
}

void cancelBooking() {

    int bid;

    cout << " Enter Booking ID: ";
    cin >> bid;

    for(int i = 0; i < (int)bookings.size(); i++) {

        if(bookings[i].getId() == bid) {

            for(int j = 0; j < (int)seats.size(); j++) {

                if(seats[j].getLabel() == bookings[i].getSeat())
                    seats[j].setBooked(false);
            }

            cout << " Booking #" << bid << " cancelled.\n";

            bookings.erase(bookings.begin() + i);

            return;
        }
    }

    cout << " Booking not found!\n";
}

void viewBooking() {

    int bid;

    cout << " Enter Booking ID: ";
    cin >> bid;

    for(int i = 0; i < (int)bookings.size(); i++) {

        if(bookings[i].getId() == bid) {

            cout << "\n--- Booking Receipt ---\n";

            cout << " ID       : #" << bookings[i].getId() << "\n";
            cout << " Customer : " << bookings[i].getCustomer() << "\n";
            cout << " Movie    : " << bookings[i].getMovie() << "\n";

            cout << " Seat     : "
                 << bookings[i].getSeat()
                 << " (" << bookings[i].getType() << ")\n";

            cout << " Amount   : Rs."
                 << bookings[i].getPrice() << "\n";

            cout << "-----------------------\n";

            return;
        }
    }

    cout << " Booking not found!\n";
}

int main() {

    init();

    int ch;

    do {

        cout << "\n===== CINEMAX BOOKING SYSTEM =====\n";

        cout << " 1. View Movies & Book\n";
        cout << " 2. View Seats & Book\n";
        cout << " 3. Book Ticket Directly\n";
        cout << " 4. View Booking Receipt\n";
        cout << " 5. Cancel Booking\n";
        cout << " 0. Exit\n";

        cout << " Choice: ";
        cin >> ch;

        cout << "\n";

        if(ch == 1)
            viewMoviesMenu();

        else if(ch == 2)
            viewSeatsMenu();

        else if(ch == 3)
            bookTicket();

        else if(ch == 4)
            viewBooking();

        else if(ch == 5)
            cancelBooking();

        else if(ch == 0)
            cout << " Goodbye!\n";

        else
            cout << " Invalid choice!\n";

    } while(ch != 0);

    return 0;
}
