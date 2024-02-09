# Course-Project-Pt.3
**This respiratory is to provide the file for the program project in CIS-18A**









import java.io.FileWriter;
import java.io.IOException;
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;
import javax.swing.JOptionPane;

class Product {
    String name;
    double price;

    // Constructor to initialize name and price of the product
    Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
}

class Appointment {
    LocalDate date;
    LocalTime time;

    // Constructor to initialize date and time of the appointment
    Appointment(LocalDate date, LocalTime time) {
        this.date = date;
        this.time = time;
    }
}

public class CafeOrder {
    public static void main(String[] args) {
        // Create a list to store available products with their names and prices
        List<Product> products = new ArrayList<>();
        products.add(new Product("Small Latte", 3.50));
        products.add(new Product("Large Latte", 4.50));
        // ... add more products here ...

        // Create a menu to display available products and their prices to the user
        StringBuilder menu = new StringBuilder("Welcome to Arianna's Cafe!\nHere's our Menu:\n");
        for (int i = 0; i < products.size(); i++) {
            Product product = products.get(i);
            menu.append((i + 1)).append(". ").append(product.name).append(" - $").append(String.format("%.2f", product.price)).append("\n");
        }

        // Create lists to store selected products and calculate total price
        List<Product> selectedProducts = new ArrayList<>();
        double totalPrice = 0;

        // Loop to allow user to select products until they're done
        boolean addingMore = true;
        while (addingMore) {
            // Prompt user to select a product from the menu
            String choiceStr = JOptionPane.showInputDialog(null, menu.toString() + "\nEnter the number of the item you'd like to order (1-" + products.size() + "):");
            int choice = Integer.parseInt(choiceStr);
            if (choice < 1 || choice > products.size()) {
                // Display error message for invalid selection and continue loop
                JOptionPane.showMessageDialog(null, "Invalid option. Please try again.");
                continue;
            }

            // Add selected product to the order and update total price
            Product selectedProduct = products.get(choice - 1);
            selectedProducts.add(selectedProduct);
            totalPrice += selectedProduct.price;

            // Ask user if they want to add more items to their order
            int option = JOptionPane.showConfirmDialog(null, "Would you like to add anything else to your order?", "Add More Items?", JOptionPane.YES_NO_OPTION);
            addingMore = option == JOptionPane.YES_OPTION;
        }

        // Prompt user to enter preferred pickup date
        String dateString = JOptionPane.showInputDialog(null, "Enter your preferred pickup date (YYYY-MM-DD):");
        LocalDate date = LocalDate.parse(dateString);

        // Prompt user to enter preferred pickup time within cafe's operating hours
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("hh:mm a");
        LocalTime time = null;
        boolean isValidTime;
        do {
            String timeString = JOptionPane.showInputDialog(null, "Enter your preferred pickup time (HH:MM AM/PM):");
            try {
                time = LocalTime.parse(timeString, formatter);
                if (time.isBefore(LocalTime.of(7, 0)) || time.isAfter(LocalTime.of(19, 0))) {
                    // Display error message for invalid time and continue loop
                    JOptionPane.showMessageDialog(null, "Sorry! But we're closed during that time. Please select a time between 7am - 7pm.");
                    isValidTime = false;
                } else {
                    isValidTime = true;
                }
            } catch (Exception e) {
                // Display error message for invalid time format and continue loop
                JOptionPane.showMessageDialog(null, "Invalid time format. Please enter the time in HH:MM AM/PM format.");
                isValidTime = false;
            }
        } while (!isValidTime);

        // Create an appointment object with selected date and time
        Appointment appointment = new Appointment(date, time);

        // Create a summary of the user's order including selected items, total price, and pickup details
        StringBuilder orderSummary = new StringBuilder("Your Order Summary:\n");
        for (Product product : selectedProducts) {
            orderSummary.append("Item: ").append(product.name).append("\n");
            orderSummary.append("Price: $").append(String.format("%.2f", product.price)).append("\n\n");
        }
        orderSummary.append("Total Price: $").append(String.format("%.2f", totalPrice)).append("\n");
        orderSummary.append("Pickup Date: ").append(appointment.date).append("\n");
        orderSummary.append("Pickup Time: ").append(appointment.time.format(formatter));

        // Display the order summary and total price using Swing
        JOptionPane.showMessageDialog(null, orderSummary.toString(), "Order Summary", JOptionPane.INFORMATION_MESSAGE);

        // Write the order summary to a text file
        try {
            FileWriter writer = new FileWriter("order_summary.txt");
            writer.write(orderSummary.toString());
            writer.close();
            // Display thank you message for successful order
            JOptionPane.showMessageDialog(null, "Thank you for your order! ^-^");
        } catch (IOException e) {
            // Display error message if writing the order summary fails
            JOptionPane.showMessageDialog(null, "An error occurred while saving the order summary: " + e.getMessage());
        }
    }
}
