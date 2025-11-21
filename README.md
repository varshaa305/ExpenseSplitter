# ExpenseSplitter

import java.util.*;

public class ExpenseSplitter {

    private static Map<String, Double> balances = new HashMap<>();
    private static List<String> members = new ArrayList<>();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        System.out.println("===== EXPENSE SPLITTER PROJECT =====");

        while (true) {
            System.out.println("\n1. Add Member");
            System.out.println("2. Add Expense");
            System.out.println("3. Show Balances");
            System.out.println("4. Settle Up");
            System.out.println("5. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine();

            switch (choice) {
                case 1 -> addMember();
                case 2 -> addExpense();
                case 3 -> showBalances();
                case 4 -> settleUp();
                case 5 -> {
                    System.out.println("Thank you for using Expense Splitter!");
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }

    private static void addMember() {
        System.out.print("Enter member name: ");
        String name = scanner.nextLine();

        if (members.contains(name)) {
            System.out.println("Member already exists!");
            return;
        }

        members.add(name);
        balances.put(name, 0.0);
        System.out.println("Added member: " + name);
    }

    private static void addExpense() {
        System.out.print("Enter payer name: ");
        String payer = scanner.nextLine();

        if (!members.contains(payer)) {
            System.out.println("Payer is not a member!");
            return;
        }

        System.out.print("Enter total expense amount: ₹");
        double amount = scanner.nextDouble();
        scanner.nextLine();

        System.out.print("Enter participants (comma separated): ");
        String[] participants = scanner.nextLine().split(",");

        double share = amount / participants.length;
        balances.put(payer, balances.get(payer) + amount);

        for (String p : participants) {
            p = p.trim();
            if (!members.contains(p)) {
                System.out.println("Participant " + p + " not found, skipping...");
                continue;
            }
            balances.put(p, balances.get(p) - share);
        }

        System.out.println("Expense of ₹" + amount + " added successfully.");
        System.out.println("Each share: ₹" + String.format("%.2f", share));
    }

    private static void showBalances() {
        System.out.println("\n----- CURRENT BALANCES -----");
        for (String name : members) {
            double balance = balances.get(name);
            if (balance > 0)
                System.out.println(name + " should RECEIVE ₹" + String.format("%.2f", balance));
            else if (balance < 0)
                System.out.println(name + " should PAY ₹" + String.format("%.2f", Math.abs(balance)));
            else
                System.out.println(name + " is SETTLED.");
        }
    }

    private static void settleUp() {
        List<Map.Entry<String, Double>> creditors = new ArrayList<>();
        List<Map.Entry<String, Double>> debtors = new ArrayList<>();

        for (Map.Entry<String, Double> entry : balances.entrySet()) {
            if (entry.getValue() > 0)
                creditors.add(entry);
            else if (entry.getValue() < 0)
                debtors.add(entry);
        }

        System.out.println("\n----- SETTLEMENTS -----");
        int i = 0, j = 0;

        while (i < debtors.size() && j < creditors.size()) {
            String debtor = debtors.get(i).getKey();
            double debtAmount = Math.abs(debtors.get(i).getValue());

            String creditor = creditors.get(j).getKey();
            double creditAmount = creditors.get(j).getValue();

            double settleAmount = Math.min(debtAmount, creditAmount);

            System.out.println(debtor + " should pay ₹" + String.format("%.2f", settleAmount) + " to " + creditor);

            debtors.get(i).setValue(-(debtAmount - settleAmount));
            creditors.get(j).setValue(creditAmount - settleAmount);

            if (debtAmount > creditAmount)
                j++;
            else
                i++;
        }
        System.out.println("------------------------");
    }
}
