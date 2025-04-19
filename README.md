
package com.example.railwayreservation.model;

public class Train {
    private String name;
    private String time;
    private int passengerStrength;
    private int trainNumber;

    public Train(String name, String time, int passengerStrength, int trainNumber) {
        this.name = name;
        this.time = time;
        this.passengerStrength = passengerStrength;
        this.trainNumber = trainNumber;
    }

    // Getters and setters
    public String getName() { return name; }
    public String getTime() { return time; }
    public int getPassengerStrength() { return passengerStrength; }
    public int getTrainNumber() { return trainNumber; }

    public void setName(String name) { this.name = name; }
    public void setTime(String time) { this.time = time; }
    public void setPassengerStrength(int passengerStrength) { this.passengerStrength = passengerStrength; }
    public void setTrainNumber(int trainNumber) { this.trainNumber = trainNumber; }
}
 
ReservationService.java (service)
package com.example.railwayreservation.service;

import com.example.railwayreservation.model.Train;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class ReservationService {
    private final List<Train> availableTrains = new ArrayList<>();
    private final List<String> bookedSeats = new ArrayList<>();

    public ReservationService() {
        availableTrains.add(new Train("Dhaka - Chottogram", "13:05", 50, 1010));
        availableTrains.add(new Train("Dhaka - Rongpur", "7:00", 50, 2013));
        availableTrains.add(new Train("Rajshai - Dhaka", "10:00", 50, 3045));
    }

    public List<Train> getAvailableTrains() {
        return availableTrains;
    }

    public String checkSeatAvailability(int trainNumber) {
        for (Train train : availableTrains) {
            if (train.getTrainNumber() == trainNumber) {
                int availableSeats = train.getPassengerStrength() - bookedSeats.size();
                return "Available seats on Train " + trainNumber + ": " + availableSeats;
            }
        }
        return "Train not found.";
    }

    public String bookTicket(int trainNumber, String passengerName) {
        for (Train train : availableTrains) {
            if (train.getTrainNumber() == trainNumber) {
                if (bookedSeats.size() < train.getPassengerStrength()) {
                    bookedSeats.add(passengerName);
                    return "Ticket booked successfully for " + passengerName;
                } else {
                    return "Sorry, the train is fully booked.";
                }
            }
        }
        return "Train not found.";
    }

    public String cancelTicket(String passengerName) {
        if (bookedSeats.remove(passengerName)) {
            return "Ticket canceled successfully for " + passengerName;
        } else {
            return "Passenger not found or no booking exists for this passenger.";
        }
    }

    public List<String> getBookedSeats() {
        return bookedSeats;
    }
}















package com.example.railwayreservation.controller;

import com.example.railwayreservation.model.Train;
import com.example.railwayreservation.service.ReservationService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api")
public class ReservationController {

    @Autowired
    private ReservationService service;

    @GetMapping("/trains")
    public List<Train> getTrains() {
        return service.getAvailableTrains();
    }

    @GetMapping("/trains/{trainNumber}/availability")
    public String checkAvailability(@PathVariable int trainNumber) {
        return service.checkSeatAvailability(trainNumber);
    }

    @PostMapping("/trains/{trainNumber}/book")
    public String bookTicket(@PathVariable int trainNumber, @RequestParam String passengerName) {
        return service.bookTicket(trainNumber, passengerName);
    }

    @DeleteMapping("/cancel")
    public String cancelTicket(@RequestParam String passengerName) {
        return service.cancelTicket(passengerName);
    }

    @GetMapping("/booked")
    public List<String> getBookedTickets() {
        return service.getBookedSeats();
    }
} 

package com.example.railwayreservation;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RailwayReservationApplication {

    public static void main(String[] args) {
        SpringApplication.run(RailwayReservationApplication.class, args);
    }
} 
