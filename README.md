
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalFinanceApp
{
    class Record
    {
        public string Details { get; set; }
        public decimal Value { get; set; }
        public string Category { get; set; }
        public string Kind { get; set; }
        public DateTime EntryDate { get; set; }

        public Record(string details, decimal value, string kind, string category, DateTime date)
        {
            Details = details;
            Value = value;
            Kind = kind;
            Category = category;
            EntryDate = date;
        }
    }

    class FinanceManager
    {
        private List<Record> records = new List<Record>();

        public void InsertRecord(Record r)
        {
            records.Add(r);
        }

        public decimal CalculateIncome()
        {
            return records
                .Where(r => r.Kind.Equals("Income", StringComparison.OrdinalIgnoreCase))
                .Sum(r => r.Value);
        }

        public decimal CalculateExpenses()
        {
            return records
                .Where(r => r.Kind.Equals("Expense", StringComparison.OrdinalIgnoreCase))
                .Sum(r => r.Value);
        }

        public decimal ComputeBalance()
        {
            return CalculateIncome() - CalculateExpenses();
        }

        public void DisplayCategoryBreakdown()
        {
            Console.WriteLine("\nCategory Spending Breakdown:");
            var categoryGroups = records
                .Where(r => r.Kind.Equals("Expense", StringComparison.OrdinalIgnoreCase))
                .GroupBy(r => r.Category);

            if (!categoryGroups.Any())
            {
                Console.WriteLine("No expense records to display.");
                return;
            }

            foreach (var group in categoryGroups)
            {
                decimal total = group.Sum(r => r.Value);
                Console.WriteLine($"{group.Key}: {new string('#', (int)(total / 100))} ({total})");
            }
        }

        public void ListByValue()
        {
            var sorted = records.OrderByDescending(r => r.Value).ToList();
            Console.WriteLine("\nTransactions Ordered by Value:");
            foreach (var r in sorted)
            {
                Console.WriteLine($"{r.EntryDate:yyyy-MM-dd} | {r.Kind} | {r.Details} | {r.Category} | {r.Value}");
            }
        }
    }

    class App
    {
        static void Main()
        {
            FinanceManager manager = new FinanceManager();
            bool active = true;

            Console.WriteLine("============ Budget Tracker ============");

            while (active)
            {
                Console.WriteLine("\nOptions:\n1. Enter New Record\n2. View Summary\n3. Category Breakdown\n4. Sort Records by Value\n5. Quit");
                Console.Write("Your choice: ");
                string option = Console.ReadLine();

                switch (option)
                {
                    case "1":
                        Console.Write("Enter Description: ");
                        string detail = Console.ReadLine().Trim();

                        Console.Write("Enter Amount: ");
                        if (!decimal.TryParse(Console.ReadLine(), out decimal val))
                        {
                            Console.WriteLine("Invalid amount. Please enter a number.");
                            break;
                        }

                        Console.Write("Type (Income/Expense): ");
                        string kindInput = Console.ReadLine().Trim().ToLower();

                        string kind;
                        if (kindInput == "income")
                            kind = "Income";
                        else if (kindInput == "expense")
                            kind = "Expense";
                        else
                        {
                            Console.WriteLine("Invalid type. Must be 'Income' or 'Expense'.");
                            break;
                        }

                        Console.Write("Enter Category: ");
                        string cat = Console.ReadLine().Trim();

                        Console.Write("Date (yyyy-mm-dd): ");
                        if (!DateTime.TryParse(Console.ReadLine(), out DateTime date))
                        {
                            Console.WriteLine("Invalid date format.");
                            break;
                        }

                        manager.InsertRecord(new Record(detail, val, kind, cat, date));
                        Console.WriteLine("Record successfully saved.");
                        break;

                    case "2":
                        Console.WriteLine($"\nTotal Income: {manager.CalculateIncome()}");
                        Console.WriteLine($"Total Expenses: {manager.CalculateExpenses()}");
                        Console.WriteLine($"Balance: {manager.ComputeBalance()}");
                        break;

                    case "3":
                        manager.DisplayCategoryBreakdown();
                        break;

                    case "4":
                        manager.ListByValue();
                        break;

                    case "5":
                        Console.WriteLine("Goodbye and stay financially smart!");
                        active = false;
                        break;

                    default:
                        Console.WriteLine("Invalid option. Please select a number between 1 and 5.");
                        break;
                }
            }
        }
    }
}

