# DCIT-201
Assignment 
package com.vehicleRental;

public abstract class Vehicle {
    // Encapsulated fields
    private String vehicleId;
    private String model;
    private double baseRentalRate;
    private boolean isAvailable;

    // Constructor
    public Vehicle(String vehicleId, String model, double baseRentalRate) {
        this.vehicleId = vehicleId;
        this.model = model;
        this.baseRentalRate = baseRentalRate;
        this.isAvailable = true; // Default availability
    }

    // Getters and Setters
    public String getVehicleId() {
        return vehicleId;
    }

    public String getModel() {
        return model;
    }

    public double getBaseRentalRate() {
        return baseRentalRate;
    }

    public boolean isAvailable() {
        return isAvailable;
    }

    public void setAvailable(boolean available) {
        this.isAvailable = available;
    }

    // Abstract methods
    public abstract double calculateRentalCost(int days);

    public abstract boolean isAvailableForRental();

    @Override
    public String toString() {
        return "Vehicle ID: " + vehicleId + ", Model: " + model +
               ", Base Rate: $" + baseRentalRate + ", Available: " + isAvailable;
    }
}

package com.vehicleRental;

public class Car extends Vehicle {

    // Constructor
    public Car(String vehicleId, String model, double baseRentalRate) {
        super(vehicleId, model, baseRentalRate);
    }

    // Implement abstract methods
    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days * 1.2; // 20% additional charge for cars
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }
}


package com.vehicleRental;

public class Motorcycle extends Vehicle {

    // Constructor
    public Motorcycle(String vehicleId, String model, double baseRentalRate) {
        super(vehicleId, model, baseRentalRate);
    }

    // Implement abstract methods
    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days; // No additional charge
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }
}

package com.vehicleRental;

public class Truck extends Vehicle {

    // Constructor
    public Truck(String vehicleId, String model, double baseRentalRate) {
        super(vehicleId, model, baseRentalRate);
    }

    // Implement abstract methods
    @Override
    public double calculateRentalCost(int days) {
        return getBaseRentalRate() * days * 1.5; // 50% additional charge for trucks
    }

    @Override
    public boolean isAvailableForRental() {
        return isAvailable();
    }
}



package com.vehicleRental;

public class Customer {
    // Encapsulated fields
    private String name;
    private String email;
    private int loyaltyPoints; // Points for discounts or rewards

    // Constructor
    public Customer(String name, String email) {
        this.name = name;
        this.email = email;
        this.loyaltyPoints = 0; // Default points
    }

    // Getters and Setters
    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public int getLoyaltyPoints() {
        return loyaltyPoints;
    }

    public void addLoyaltyPoints(int points) {
        if (points > 0) {
            this.loyaltyPoints += points;
        }
    }

    public void redeemPoints(int points) {
        if (points <= loyaltyPoints) {
            this.loyaltyPoints -= points;
        } else {
            System.out.println("Insufficient points to redeem!");
        }
    }

    @Override
    public String toString() {
        return "Customer Name: " + name + ", Email: " + email +
               ", Loyalty Points: " + loyaltyPoints;
    }
}


package com.vehicleRental;

import java.time.LocalDate;

public class RentalTransaction {
    private String transactionId;
    private String vehicleId;
    private String customerName;
    private int rentalDays;
    private double totalCost;
    private LocalDate rentalDate;

    // Constructor
    public RentalTransaction(String transactionId, String vehicleId, String customerName,
                             int rentalDays, double totalCost) {
        this.transactionId = transactionId;
        this.vehicleId = vehicleId;
        this.customerName = customerName;
        this.rentalDays = rentalDays;
        this.totalCost = totalCost;
        this.rentalDate = LocalDate.now(); // Automatically set to current date
    }

    // Getters
    public String getTransactionId() {
        return transactionId;
    }

    public String getVehicleId() {
        return vehicleId;
    }

    public String getCustomerName() {
        return customerName;
    }

    public int getRentalDays() {
        return rentalDays;
    }

    public double getTotalCost() {
        return totalCost;
    }

    public LocalDate getRentalDate() {
        return rentalDate;
    }

    @Override
    public String toString() {
        return "Transaction ID: " + transactionId + ", Vehicle ID: " + vehicleId +
               ", Customer: " + customerName + ", Days: " + rentalDays +
               ", Cost: $" + totalCost + ", Date: " + rentalDate;
    }
}


package com.vehicleRental;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class RentalAgency {
    private List<Vehicle> vehicleFleet; // List of all vehicles
    private List<RentalTransaction> transactions; // List of all transactions

    // Constructor
    public RentalAgency() {
        this.vehicleFleet = new ArrayList<>();
        this.transactions = new ArrayList<>();
    }

    // Add a vehicle to the fleet
    public void addVehicle(Vehicle vehicle) {
        vehicleFleet.add(vehicle);
    }

    // Display all available vehicles
    public void displayAvailableVehicles() {
        for (Vehicle vehicle : vehicleFleet) {
            if (vehicle.isAvailable()) {
                System.out.println(vehicle);
            }
        }
    }

    // Get a vehicle by ID
    public Vehicle getVehicleById(String vehicleId) {
        for (Vehicle vehicle : vehicleFleet) {
            if (vehicle.getVehicleId().equals(vehicleId)) {
                return vehicle;
            }
        }
        return null;
    }

    // Process a rental
    public void processRental(String vehicleId, Customer customer, int days) {
        Vehicle vehicle = getVehicleById(vehicleId);
        if (vehicle != null && vehicle.isAvailableForRental()) {
            double cost = vehicle.calculateRentalCost(days);
            vehicle.setAvailable(false); // Mark vehicle as rented

            // Add loyalty points to the customer
            int points = (int) (cost / 10); // Earn 1 point for every $10 spent
            customer.addLoyaltyPoints(points);

            // Create a rental transaction
            RentalTransaction transaction = new RentalTransaction(
                UUID.randomUUID().toString(), vehicleId, customer.getName(), days, cost);
            transactions.add(transaction);

            System.out.println("Rental processed successfully for " + customer.getName() +
                               ". Total cost: $" + cost);
        } else {
            System.out.println("Vehicle is not available for rental.");
        }
    }

    // Return a vehicle
    public void returnVehicle(String vehicleId) {
        Vehicle vehicle = getVehicleById(vehicleId);
        if (vehicle != null) {
            vehicle.setAvailable(true); // Mark vehicle as available
            System.out.println("Vehicle returned successfully.");
        } else {
            System.out.println("Invalid vehicle ID.");
        }
    }

    // Display all rental transactions
    public void displayTransactions() {
        for (RentalTransaction transaction : transactions) {
            System.out.println(transaction);
        }
    }
}


