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

        // User input for additional allowances
        System.out.print("Enter Rice Subsidy (monthly): PHP ");
        double riceSubsidy = scanner.nextDouble();

        System.out.print("Enter Phone Allowance (monthly): PHP ");
        double phoneAllowance = scanner.nextDouble();

        System.out.print("Enter Clothing Allowance (monthly): PHP ");
        double clothingAllowance = scanner.nextDouble();

        scanner.nextLine(); // Consume newline

        final double LUNCH_BREAK_HOURS = 1.0;

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

            // Compute total worked hours after lunch break deduction
            Duration duration = Duration.between(timeIn, timeOut);
            long hoursWorked = duration.toHours();
            long minutesWorked = duration.toMinutes() % 60;
            double totalWorkedHours = (hoursWorked + (minutesWorked / 60.0)) - LUNCH_BREAK_HOURS;

            // Ensure no negative hours
            if (totalWorkedHours < 0) {
                totalWorkedHours = 0;
            }

            // Compute daily salary using totalWorkedHours (AFTER lunch break deduction)
            double dailySalary = totalWorkedHours * hourlyRate;
            totalWeeklySalary += dailySalary;

            // Display daily results
            System.out.println("Time In: " + timeIn);
            System.out.println("Time Out: " + timeOut);
            System.out.println("Total Hours Worked: " + hoursWorked);
            System.out.println("Total Minutes Worked: " + minutesWorked);
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

        // Compute Net Monthly Salary
        double totalMonthlyDeductions = (tax / 12) + philHealth + sss + pagIbig;
        double netMonthlySalary = basicPay + riceSubsidy + phoneAllowance + clothingAllowance - totalMonthlyDeductions;

        // Compute net weekly salary (before monthly allowances are applied)
        double netWeeklySalary = totalWeeklySalary - (tax / 52);

        // Display weekly summary
        System.out.println("\nWeekly Payroll Summary");
        System.out.println("----------------------");
        System.out.println("Total Weekly Salary: PHP " + String.format("%.2f", totalWeeklySalary));
        System.out.println("\nMonthly Deductions:");
        System.out.println("- Tax (computed annually, displayed monthly): PHP " + String.format("%.2f", tax / 12));
        System.out.println("- PhilHealth (5% monthly): PHP " + String.format("%.2f", philHealth));
        System.out.println("- SSS (15% monthly): PHP " + String.format("%.2f", sss));
        System.out.println("- Pag-IBIG (2% monthly): PHP " + String.format("%.2f", pagIbig));

        // Display Allowances
        System.out.println("\nMonthly Allowances:");
        System.out.println("- Rice Subsidy: PHP " + String.format("%.2f", riceSubsidy));
        System.out.println("- Phone Allowance: PHP " + String.format("%.2f", phoneAllowance));
        System.out.println("- Clothing Allowance: PHP " + String.format("%.2f", clothingAllowance));

        System.out.println("\nNet Monthly Salary (After Deductions and Allowances): PHP " + String.format("%.2f", netMonthlySalary));
        System.out.println("Net Weekly Salary (Before Monthly Allowances): PHP " + String.format("%.2f", netWeeklySalary));
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
