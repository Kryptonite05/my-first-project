# my-first-project
Analytic comprog

import java.time.LocalTime;
import java.time.Duration;
import java.util.Scanner;

public class PayrollSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
      
        // User input for basic pay (monthly salary)
        System.out.print("Enter basic pay (monthly salary): PHP ");
        double basicPay = scanner.nextDouble();
        
        // User input for hourly rate
        System.out.print("Enter hourly rate: PHP ");
        double hourlyRate = scanner.nextDouble();
        scanner.nextLine(); // Consume newline
        
        final double LUNCH_BREAK_HOURS = 1.0; // Fixed lunch break deduction
        
        String[] days = {"Monday", "Tuesday", "Wednesday", "Thursday", "Friday"};
        double totalWeeklySalary = 0;
        
        for (String day : days) {
            System.out.println("\n" + day);
            
            // User input for time in
            System.out.print("Enter time in (HH:MM): ");
            LocalTime timeIn = parseTime(scanner.nextLine());
            
            // User input for time out
            System.out.print("Enter time out (HH:MM): ");
            LocalTime timeOut = parseTime(scanner.nextLine());
            
            // Compute total worked hours
            Duration duration = Duration.between(timeIn, timeOut);
            long hoursWorked = duration.toHours();
            long minutesWorked = duration.toMinutes() % 60;

            // Convert minutes to fraction of an hour
            double totalWorkedHours = (hoursWorked + (minutesWorked / 60.0)) - LUNCH_BREAK_HOURS;

            // Ensure no negative hours
            if (totalWorkedHours < 0) {
                totalWorkedHours = 0;
            }

            // Compute daily salary using totalWorkedHours
            double dailySalary = totalWorkedHours * hourlyRate;
            totalWeeklySalary += dailySalary;

            // Display daily results
            System.out.println("Time In: " + timeIn);
            System.out.println("Time Out: " + timeOut);
            System.out.println("Total Hours Worked: " + totalWorkedHours);
            System.out.println("Salary for " + day + ": PHP " + String.format("%.2f", dailySalary));
        }
        
        scanner.close();
        
        // Compute deductions
        double annualSalary = basicPay * 12; // Convert monthly salary to annual salary
        double tax = computeTax(annualSalary); // Compute tax based on full annual salary
        
        // Compute PhilHealth (5% of monthly salary, min ₱500, max ₱2,500)
        double philHealth = Math.max(500, Math.min(2500, (basicPay * 0.05)));
        
        // Compute SSS (15% of monthly salary)
        double sss = basicPay * 0.15;
        
        // Compute Pag-IBIG (2% of monthly salary)
        double pagIbig = basicPay * 0.02;
        
        double netSalary = totalWeeklySalary - (tax / 52); // Tax still applied weekly

        // Display weekly summary
        System.out.println("\nWeekly Payroll Summary");
        System.out.println("----------------------");
        System.out.println("Total Weekly Salary: PHP " + String.format("%.2f", totalWeeklySalary));
        System.out.println("\nMonthly Deductions:");
        System.out.println("- Tax (computed annually, displayed monthly): PHP " + String.format("%.2f", tax / 12));
        System.out.println("- PhilHealth (5% monthly): PHP " + String.format("%.2f", philHealth));
        System.out.println("- SSS (15% monthly): PHP " + String.format("%.2f", sss));
        System.out.println("- Pag-IBIG (2% monthly): PHP " + String.format("%.2f", pagIbig));
        System.out.println("\nNet Weekly Salary (Before Monthly Deductions): PHP " + String.format("%.2f", netSalary));
    }
    
    // Method to compute tax based on annual salary
    private static double computeTax(double annualSalary) {
        if (annualSalary <= 250000) {
            return 0;
        } else if (annualSalary <= 400000) {
            return (annualSalary - 250000) * 0.15;
        } else if (annualSalary <= 800000) {
            return 22500 + (annualSalary - 400000) * 0.20;
        } else if (annualSalary <= 2000000) {
            return 102500 + (annualSalary - 800000) * 0.25;
        } else if (annualSalary <= 8000000) {
            return 402500 + (annualSalary - 2000000) * 0.30;
        } else {
            return 2202500 + (annualSalary - 8000000) * 0.35;
        }
    }

    // Method to parse time from string to LocalTime
    private static LocalTime parseTime(String timeStr) {
        try {
            String[] parts = timeStr.split(":");
            int hours = Integer.parseInt(parts[0]);
            int minutes = Integer.parseInt(parts[1]);
            return LocalTime.of(hours, minutes);
        } catch (Exception e) {
            throw new IllegalArgumentException("Invalid time format. Please use HH:MM.");
        }
    }
}
