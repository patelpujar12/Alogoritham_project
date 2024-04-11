
using System.Net;
using System.Numerics;
using System.Xml.Linq;
using static System.Formats.Asn1.AsnWriter;
using static System.Reflection.Metadata.BlobBuilder;

namespace Library_Toala_Patel
{
    internal class Program
    {
        //CONSTANTS
        const int ELEMENTSCLIENT = 10;
        const int ELEMENTSEMPLOYEE = 1;
        const int ELEMENTSBOOK = 100;
        const int ELEMENTSINVOICE = 100;
        //VARIABLES
        static bool booksFound = false, validateArrayClient = false, validateArrayBook = false;


        static int iDisplayClient = 0, iDisplayBook = 0, iDisplayEmployee = 0, iInsertEmployee = 0, iInsertClient = 0, iInsertBook = 0, vBookID, vClientID, vbookid, vEmployeeID, vInvoice;
        static string vClientPass = "", vEmployeePass = "", vAuthor = "";
        static Employee[] employees = new Employee[ELEMENTSEMPLOYEE];
        static Client[] clients = new Client[ELEMENTSCLIENT];
        static Book[] books = new Book[ELEMENTSBOOK];
        static Invoice[] invoices = new Invoice[ELEMENTSINVOICE];


        struct Employee
        {
            public int Id;
            public string FullName;
            public string Password;

            public Employee(int id, string name, string pass)
            {
                Id = id;
                FullName = name;
                Password = pass;
            }
        }

        struct Client
        {
            public int Id;
            public string FullName;
            public string Password;

            public Client(int id, string name, string pass)
            {
                Id = id;
                FullName = name;
                Password = pass;
            }
        }

        struct Book
        {
            public int Id;
            public string Name;
            public string Author;
            public int YearOfPublication;
            public double UnitPrice;
            public int Quantity;
            public Book(int id, string name, string author, int yearofpublication, double unitprice, int quantity)
            {
                Id = id;
                Name = name;
                Author = author;
                YearOfPublication = yearofpublication;
                UnitPrice = unitprice;
                Quantity = quantity;
            }
        }
        /*    struct sell
            {
                internal bool IsSold;
                public int bookId;
                public int soldBooks;

                public (bool IsSold, int bookId, int soldBooks)
                 {

                    }
    *//*
            }
    */
        struct Invoice
        {
            public int Id;
            public int ClientId;
            public int BookId;
            public int Quantity;
            public double UnitPrice;
            public double Total;
            public double TotalInvoice;

            public Invoice(int id, int clientId, int bookId, int quantity, double price, double total, double totalInvoice)
            {
                Id = id;
                ClientId = clientId;
                BookId = bookId;
                Quantity = quantity;
                UnitPrice = price;
                Total = total;
                TotalInvoice = totalInvoice;
            }
        }

        static void Main(string[] args)
        {
            CreateEmployee(111111, "Employee Test", "tester");
            CreateClient(111, "Default Client 111", "123");
            CreateClient(1, "Default Client 1", "123");
            FillDefaultBook();
            FillDefaultInvoice(); //Temporarily for testing only

            while (true)
            {
                DisplayMainMenu();
                int option = ReadOption(3);

                switch (option)
                {
                    case 1:
                        Console.Clear();
                        Console.WriteLine("------------------------------------");
                        Console.WriteLine("     CLIENT SIGN IN        ");
                        Console.WriteLine("------------------------------------");
                        Console.Write("Insert Client ID: ");
                        vClientID = ReadInteger();
                        Console.Write("Insert Password: ");
                        vClientPass = ReadString();
                        if (ValidateClientPass(vClientID, vClientPass))
                        {
                            bool ContinueMenuClient = true;
                            while (ContinueMenuClient)
                            {
                                Console.Clear();
                                DisplayClientMenu();
                                int optionClient = ReadOption(4); // Validates the maximum value for the menu option
                                switch (optionClient)
                                {
                                    case 1:
                                        Console.WriteLine("");
                                        Console.WriteLine("1.- Display all the books availables");
                                        Console.WriteLine("");
                                        DisplayAllBooksAvailable();
                                        Console.ReadLine();
                                        break;
                                    case 2:
                                        Console.WriteLine("");
                                        Console.WriteLine("1.- Display all the books availables by Author");
                                        Console.WriteLine("");
                                        Console.Write("   Please Insert Author to search: ");
                                        vAuthor = ReadString();
                                        DisplayAllBooksByAuthor(vAuthor);
                                        Console.ReadLine();
                                        break;
                                    case 3:
                                        Console.WriteLine("2.- Display the books I have purchased");
                                        Console.WriteLine("");
                                        DisplayPurchasedBooksbyClient(vClientID);
                                        if (!booksFound)
                                        {
                                            Console.WriteLine("");
                                            Console.WriteLine("    The Client does not have books purchased!");
                                        }
                                        Console.ReadLine();
                                        break;
                                    case 4:
                                        ContinueMenuClient = false;
                                        break;
                                }
                            }
                        }
                        else
                        {
                            Console.WriteLine("");
                            Console.WriteLine("    The Client does not exists or password is incorrect, please try again!");
                        }
                        break;

                    case 2:
                        Console.Clear();
                        Console.Clear();
                        Console.WriteLine("------------------------------------");
                        Console.WriteLine("     EMPLOYEE SIGN IN        ");
                        Console.WriteLine("------------------------------------");
                        Console.Write("Insert Employee ID: ");
                        vEmployeeID = ReadInteger();
                        Console.Write("Insert Password: ");
                        vEmployeePass = ReadString();

                        if (ValidateEmployeePass(vEmployeeID, vEmployeePass))
                        {
                            //Console.WriteLine("");
                            bool ContinueMenuEmployee = true;
                            while (ContinueMenuEmployee)
                            {
                                Console.Clear();
                                DisplayEmployeeMenu();
                                int optionEmployee = ReadOption(10); // Validates the maximum value for the menu option
                                switch (optionEmployee)
                                {
                                    case 1:
                                        // Option 1: Create a new cliente. Validates if the cliente exists and if the array is complete
                                        Console.WriteLine("");
                                        Console.WriteLine("1.- Create a Client");
                                        if (iInsertClient == ELEMENTSCLIENT)
                                        {
                                            Console.WriteLine("");
                                            Console.WriteLine("The Client array is complete");
                                        }

                                        else
                                        {
                                            Console.Write("    Please enter Client Id # " + iInsertClient + ": ");
                                            vClientID = ReadInteger();
                                            if (ClientExist(vClientID))
                                            {
                                                Console.WriteLine("");
                                                Console.WriteLine("    The client exists, please enter a new client");
                                            }
                                            else
                                            {
                                                Console.Write("    Please enter Client Name # " + iInsertClient + ": ");
                                                string vName = ReadString();
                                                Console.Write("    Please enter Client Password # " + iInsertClient + ": ");
                                                string vPassword = ReadString();

                                                if (CreateClient(vClientID, vName, vPassword))
                                                {
                                                    Console.WriteLine("");
                                                    Console.WriteLine("    Client created");
                                                    Console.WriteLine("");
                                                    validateArrayClient = true;
                                                }
                                                else
                                                {
                                                    Console.WriteLine("");
                                                    Console.WriteLine("    Error: Client was not created");
                                                }
                                            }
                                        }
                                        Console.ReadLine();
                                        break;

                                    case 2:
                                        Console.WriteLine("2.- Modify one client by ID");
                                        Console.WriteLine("");
                                        Console.Write("    Please enter Client Id : ");
                                        vClientID = ReadInteger();
                                        if (ClientExist(vClientID))
                                        {
                                            Console.Write("    Actual Client Name : " + clients[iDisplayClient].FullName);
                                            Console.Write("    Please enter New Client Name # " + iInsertClient + ": ");
                                            string vName = ReadString();
                                            Console.Write("    Actual Client Password : " + clients[iDisplayClient].Password);
                                            Console.Write("    Please enter New Client Password # " + iInsertClient + ": ");
                                            string vPassword = ReadString();
                                            if (ModifyClient(vClientID, vName, vPassword))
                                            {
                                                Console.WriteLine("");
                                                Console.WriteLine("    Client modified");
                                                Console.WriteLine("");
                                                validateArrayClient = true;
                                            }
                                            else
                                            {
                                                Console.WriteLine("");
                                                Console.WriteLine("    Error: Client was not modified");
                                            }
                                        }
                                        else
                                        {
                                            Console.WriteLine("");
                                            Console.WriteLine("    The client does not exists, please try again!");
                                        }
                                        Console.ReadLine();
                                        break;
                                    case 3:
                                        //Show all the clients array
                                        Console.WriteLine("3.- Display all the clients");
                                        Console.WriteLine("");
                                        DisplayAllClients();
                                        Console.ReadLine();
                                        break;
                                    case 4:
                                        Console.WriteLine("4.- Create a book");
                                        Console.WriteLine("");
                                        CreateBook();
                                        Console.ReadLine();
                                        break;
                                    case 5:
                                        Console.WriteLine("5.- Modify one book by ID");
                                        Console.WriteLine("");
                                        ModifyBookById();
                                        Console.ReadLine();
                                        break;
                                    case 6:
                                        Console.WriteLine("6.- Display all the books");
                                        DisplayAllBooks();
                                        Console.ReadLine();
                                        break;

                                    case 7:
                                        Console.WriteLine("7.- Sell a book");
                                        SellBook();
                                        Console.ReadLine();
                                        break;
                                    case 8:
                                        Console.WriteLine("8.- Display all the books sold");
                                        Console.WriteLine("");
                                        DisplayAllPurchasedBooks();
                                        Console.ReadLine();
                                        break;
                                    case 9:
                                        Console.WriteLine("9.- Display total of sales");
                                        Console.WriteLine("");
                                        DisplayTotalSales();
                                        Console.ReadLine();
                                        break;
                                    case 10:
                                        ContinueMenuEmployee = false;
                                        break;
                                }
                            }
                        }
                        else
                        {
                            Console.WriteLine("");
                            Console.WriteLine("    The Employee does not exists or password is incorrect, please try again!");
                        }
                        break;


                    case 3:
                        ExitMenu();
                        break;

                }
                Console.WriteLine("    Please enter a key");
                Console.ReadLine();
                Console.Clear();
            }


        }


        static void DisplayMainMenu()
        {
            Console.WriteLine("-------------------------------------------------");
            Console.WriteLine("       MAIN MENU       ");
            Console.WriteLine("-------------------------------------------------");
            Console.WriteLine("1) Client Sign In");
            Console.WriteLine("2) Employee Sign In");
            Console.WriteLine("3) Exit the application");
            Console.WriteLine("");
            Console.Write("Please enter your choose: ");
        }
        static void DisplayClientMenu()
        {
            Console.WriteLine("------------------------------------------------------------------------");
            Console.WriteLine("  CLIENT MENU  -  " + clients[iDisplayClient].FullName + " - " + DateTime.Now.ToString("dddd , MMM dd yyyy,hh:mm:ss"));
            Console.WriteLine("------------------------------------------------------------------------");
            Console.WriteLine("1) Books available");
            Console.WriteLine("2) Books available by Author");
            Console.WriteLine("3) Purchase");
            Console.WriteLine("4) Sign Out");
            Console.WriteLine("");
            Console.Write("Please enter your choose: ");
        }

        static void DisplayEmployeeMenu()
        {
            Console.WriteLine("------------------------------------------------------------------------");
            Console.WriteLine("    EMPLOYEE MENU  -  " + employees[0].FullName  + " - " + DateTime.Now.ToString("dddd , MMM dd yyyy,hh:mm:ss"));
            Console.WriteLine("------------------------------------------------------------------------");
            Console.WriteLine("1)  Create a client");
            Console.WriteLine("2)  Modify one client by ID");
            Console.WriteLine("3)  Display all the clients");
            Console.WriteLine("4)  Create abook");
            Console.WriteLine("5)  Modify one book by ID");
            Console.WriteLine("6)  Display all the books");
            Console.WriteLine("7)  Sell a book");
            Console.WriteLine("8)  Display all the books sold");
            Console.WriteLine("9)  Display total of sales");
            Console.WriteLine("10) Sign Out");
            Console.WriteLine("");
            Console.Write("Please enter your choose: ");
        }

        // Function that validates the integer entry and the maximum value for the menu option
        static int ReadOption(int max)
        {
            int toReturn = 0;
            while (!Int32.TryParse(Console.ReadLine(), out toReturn) || toReturn <= 0 || toReturn > max)
            {
                Console.Write("    Invalid integer value, please try again: ");
            }

            return toReturn;
        }

        static int ReadInteger()
        {
            int toReturn = 0;
            while (!Int32.TryParse(Console.ReadLine(), out toReturn) || toReturn <= 0)
            {
                Console.Write("    Invalid integer value, please try again: ");
            }

            return toReturn;
        }
        static int ReadInteger(int min, int max)
        {
            int toReturn = ReadInteger();
            while (!IsWithinRange(toReturn, min, max))
            {
                Console.Write($"The value must be between {min} and {max}. Please try again: ");
                toReturn = ReadInteger();
            }

            return toReturn;
        }
        static bool IsWithinRange(int num, int min, int max)
        {
            return num >= min && num <= max;
        }

        static double ReadDouble()
        {
            double toReturn = 0;
            while (!double.TryParse(Console.ReadLine(), out toReturn) || toReturn <= 0)
            {
                Console.Write("    Invalid double value, please try again: ");
            }

            return toReturn;
        }

        static string ReadString()
        {
            string toReturn = "";

            toReturn = Console.ReadLine();
            while (string.IsNullOrEmpty(toReturn))
            {
                Console.Write("    Please enter a valid navalue: ");
                toReturn = Console.ReadLine();

            }
            return toReturn;

        }

        static bool CreateEmployee(int employeeId, string name, string pass)
        {
            employees[iInsertEmployee].Id = employeeId;
            employees[iInsertEmployee].FullName = name;
            employees[iInsertEmployee].Password = pass;

            //iInsertEmployee = iInsertEmployee + 1;
            return true;

        }

        // Function that create a new Cliente array. 
        static bool CreateClient(int clientId, string name, string pass)
        {
            clients[iInsertClient].Id = clientId;
            clients[iInsertClient].FullName = name;
            clients[iInsertClient].Password = pass;

            iInsertClient = iInsertClient + 1;
            return true;

        }

        static bool ModifyClient(int clientId, string name, string pass)
        {
            clients[iDisplayClient].FullName = name;
            clients[iDisplayClient].Password = pass;
            return true;

        }

        static bool CreateBook(int bookId, string name, string author, int year, double price, int quantity)
        {
            books[iInsertBook].Id = bookId;
            books[iInsertBook].Name = name;
            books[iInsertBook].Author = author;
            books[iInsertBook].YearOfPublication = year;
            books[iInsertBook].UnitPrice = price;
            books[iInsertBook].Quantity = quantity;

            iInsertBook = iInsertBook + 1;
            return true;
        }
        static bool ModifyBook(int bookId, string name, string author, int year, double price, int quantity)
        {
            books[iInsertBook].Id = bookId;
            books[iInsertBook].Name = name;
            books[iInsertBook].Author = author;
            books[iInsertBook].YearOfPublication = year;
            books[iInsertBook].UnitPrice = price;
            books[iInsertBook].Quantity = quantity;
            return true;

        }

        // Function that fills the Book array whith 10 element by default
        static void FillDefaultBook()
        {
            CreateBook(10, "Little Women", "Louisa May Alcott", 2023, 25.60, 10);
            CreateBook(11, "The Great Gatsby", "F.S. Fitzgerald", 2004, 10.99, 5);
            CreateBook(12, "Dracula", "Bram Stoker", 2000, 6.99, 5);
            CreateBook(13, "The Legend of Sleepy Hollow", "Washington Irving", 2019, 11.52, 20);
            CreateBook(14, "The Chronicles of Narnia Songbook", "Hal Leonard", 2006, 14.56, 20);
            CreateBook(15, "The Lord Of The Rings", "J. R. R. Tolkien", 2003, 38.15, 10);
            //CreateBook(16, "The Secret Garden", "Frances Hodgson Burnett", 2012 ,11.29 , 10);
            CreateBook(17, "The Last of the Mohicans", "James Fenimore Cooper ", 1982, 17.49, 10);
            CreateBook(18, "To Kill a Mockingbird", "Harper Lee", 2018, 16.99, 10);
            CreateBook(19, "One Flew Over the Cuckoo's Nest", "Ken Kesey", 2012, 24.00, 10);
            CreateBook(20, "Frankenstein", "Mary", 1994, 14.00, 10);
            CreateBook(20, "Frankenstein XY", "Mary", 1996, 25.00, 25);
            //CreateBook(20, "Frankenstein", "Mary Shelley", 1994, 14.00, 10);

        }

        //Temporarily for testing only - fill Array Invoice
        static void FillDefaultInvoice()
        {
            invoices[0].Id = 1;
            invoices[0].ClientId = 1;
            invoices[0].BookId = 10;
            invoices[0].Quantity = 1;
            invoices[0].UnitPrice = 26.60;
            invoices[0].Total= 26.60;

            invoices[1].Id = 1;
            invoices[1].ClientId = 1;
            invoices[1].BookId = 11;
            invoices[1].Quantity = 2;
            invoices[1].UnitPrice = 10.99;
            invoices[1].Total = 21.98;

            invoices[2].Id = 1;
            invoices[2].ClientId = 1;
            invoices[2].BookId = 12;
            invoices[2].Quantity = 1;
            invoices[2].UnitPrice = 6.99;
            invoices[2].Total = 6.99;

            invoices[3].Id = 2;
            invoices[3].ClientId = 1;
            invoices[3].BookId = 19;
            invoices[3].Quantity = 1;
            invoices[3].UnitPrice = 24.00;
            invoices[3].Total = 24.00;

            invoices[4].Id = 2;
            invoices[4].ClientId = 1;
            invoices[4].BookId = 20;
            invoices[4].Quantity = 1;
            invoices[4].UnitPrice = 14.00;
            invoices[4].Total = 14.00;

        }

        //Function that display all Books array with in 
        static void DisplayAllBooksAvailable()
        {
            Console.WriteLine("------------------------------------------------------");
            //Console.WriteLine("INDEX\tBOOK ID\tNAME\t\t\tAUTHOR\tYEAR\tPRICE\tQUANTITY");

            for (int i = 0; i < ELEMENTSBOOK; i++)
            {
                if (books[i].Quantity > 0)
                {
                    // Console.WriteLine(i + "\t" + book[i].Id + "\t" +  book[i].Name + "\t\t\t" + book[i].Author + "\t" + book[i].Year + "\t" + book[i].Price + "\t" + book[i].Quantity);
                    Console.WriteLine("Index:" + i);
                    Console.WriteLine("Book Id:" + books[i].Id);
                    Console.WriteLine("Book Name:" + books[i].Name);
                    Console.WriteLine("Author:" + books[i].Author + "   Year:" + books[i].YearOfPublication);
                    Console.WriteLine("Price:" + books[i].UnitPrice);
                    Console.WriteLine("Quantity:" + books[i].Quantity);
                    Console.WriteLine("------------------------------------------------------");
                }
            }
        }

        //Function that display all Books availables by author   -- DON'T WORK I NEED TO CHANGE THE CODE
        static void DisplayAllBooksByAuthor(string author)
        {
            Console.WriteLine("------------------------------------------------------------------------------");
            Console.WriteLine("ID\tNAME\t\t\tAUTHOR\tYEAR\tPRICE\tQUANTITY");
            Console.WriteLine("------------------------------------------------------------------------------");

            for (int i = 0; i < ELEMENTSBOOK; i++)
            {
                if (books[i].Author == author)
                {
                    Console.WriteLine(books[i].Id + "\t" +  books[i].Name + "\t\t\t" + books[i].Author + "\t" + books[i].YearOfPublication + "\t" + books[i].UnitPrice + "\t" + books[i].Quantity);
                }
            }
            //If I have only one books with this author, it's ok, but If there are more than one books with the same author don't work, because return only the first index searched.
            //int indexFound = Array.FindIndex(books, (c) => c.Author == author);
            //if (indexFound == -1)
            //{
            //    Console.WriteLine("    There are no books for the indicated Author");
            //}
            //else
            //{
            //    //Console.WriteLine(books[indexFound].Name);
            //    Console.WriteLine("Index:" + indexFound);
            //    Console.WriteLine("Book Id:" + books[indexFound].Id);
            //    Console.WriteLine("Book Name:" + books[indexFound].Name);
            //    Console.WriteLine("Author:" + books[indexFound].Author + "   Year:" + books[indexFound].YearOfPublication);
            //    Console.WriteLine("Price:" + books[indexFound].UnitPrice);
            //    Console.WriteLine("Quantity:" + books[indexFound].Quantity);
        }

        //Function that validate if a client exists or not in Client Array and returns true or false, also returns the index where the client is saved
        static bool ClientExist(int clientid)
        {
            bool iSearch = false;
            for (int i = 0; i < ELEMENTSCLIENT; i++)
            {
                if (clients[i].Id == clientid)
                {
                    iDisplayClient = i;
                    iSearch = true;
                    break;
                }
            }
            return iSearch;
        }

        //Function that validate if a book exists or not in Book Array and returns true or false, also returns the index where the book is saved
        static bool BookExist(int bookid)
        {
            bool iSearch = false;
            for (int i = 0; i < ELEMENTSBOOK; i++)
            {
                if (books[i].Id == bookid)
                {
                    iDisplayBook = i;
                    iSearch = true;
                    break;
                }
            }
            return iSearch;
        }


        static bool ValidateClientPass(int ClientId, string Clipass)
        {
            bool iSearch = false;
            for (int i = 0; i < ELEMENTSCLIENT; i++)
            {
                if (clients[i].Id == ClientId && clients[i].Password == Clipass)
                {
                    iDisplayClient = i;
                    iSearch = true;
                    break;
                }
            }
            return iSearch;
        }

        static bool EmployeeExist(int employeeid)
        {
            bool iSearch = false;
            for (int i = 0; i < ELEMENTSCLIENT; i++)
            {
                //iSearch = false;
                if (employees[i].Id == employeeid)
                {
                    iDisplayEmployee = i;
                    iSearch = true;
                    break;
                }

            }
            return iSearch;
        }

        static bool ValidateEmployeePass(int employeeId, string Emppass)
        {
            bool iSearch = false;
            if (employees[iDisplayEmployee].Id== employeeId && employees[iDisplayEmployee].Password == Emppass)
            { iSearch = true; }
            return iSearch;
        }

        static void ExitMenu()
        {
            Console.WriteLine("");
            Console.WriteLine("Finish the Menu");
            Environment.Exit(0);
        }

        static void DisplayAllBooks()
        {
            Console.WriteLine("All books available:");
            foreach (Book book in books)
            {
                Console.WriteLine($"ID: {book.Id}, Name: {book.Name}, Author: {book.Author}, Year: {book.YearOfPublication}, Price: {book.UnitPrice}, Quantity: {book.Quantity}");
            }
        }

        // Display all books purchased by the customer who logged in.
        static void DisplayPurchasedBooksbyClient(int client)
        {
            Console.WriteLine("------------------------------------------------------------------------------------------------------");
            Console.WriteLine("INVOICE ID\t BOOK \t\t\tQUANTITY\tPRICE\tTOTAL");
            Console.WriteLine("------------------------------------------------------------------------------------------------------");

            booksFound = false;
            for (int i = 0; i < ELEMENTSINVOICE; i++)
            {
                if (invoices[i].ClientId == client)
                {
                    booksFound = true;
                    BookExist(invoices[i].BookId);
                    Console.WriteLine(invoices[i].Id + "\t\t" + invoices[i].BookId + "-" + books[iDisplayBook].Name + "\t\t" + invoices[i].Quantity + "\t\t" + invoices[i].UnitPrice + "\t\t" + invoices[i].Total);
                }
            }
        }

        // Display all books purchased - Invoice Array
        static void DisplayAllPurchasedBooks()
        {
            Console.WriteLine("------------------------------------------------------------------------------------------------------");
            Console.WriteLine("INVOICE ID\t BOOK \t\t\tQUANTITY\tPRICE\tTOTAL");
            Console.WriteLine("------------------------------------------------------------------------------------------------------");
            for (int i = 0; i < ELEMENTSINVOICE; i++)
            {
                if (invoices[i].Id != 0)
                {
                    BookExist(invoices[i].BookId);
                    Console.WriteLine(invoices[i].Id + "\t\t" + invoices[i].BookId + "-" + books[iDisplayBook].Name + "\t\t"  + invoices[i].Quantity + "\t\t" + invoices[i].UnitPrice + "\t\t" + invoices[i].Total);
                    //Console.WriteLine(invoices[i].Id + "-" + invoices[i].BookId + "-" + books[iDisplayBook].Name + "-," + invoices[i].Quantity + "-" + invoices[i].UnitPrice + "-" + invoices[i].Total);
                }
            }
        }


        //Show all Clients array
        static void DisplayAllClients()
        {
            Console.WriteLine("------------------------------------------------------");
            Console.WriteLine("ID\t FULL NAME\t\t\tPASSWORD");
            Console.WriteLine("------------------------------------------------------");
            foreach (Client client in clients)
            {
                if (client.Id != 0)
                {
                    Console.WriteLine(client.Id + "\t" + client.FullName + "\t\t" + client.Password);
                }
            }
        }

        static void CreateBook()
        {
            // Implement logic to create a new book

            if (iInsertBook == ELEMENTSBOOK)
            {
                Console.WriteLine("");
                Console.WriteLine("The book array is complete");
            }

            else
            {
                Console.Write("    Please enter Book Id # " + iInsertBook+ ": ");
                vbookid = ReadInteger();
                if (BookExist(vbookid))
                {
                    Console.WriteLine("");
                    Console.WriteLine("    The book exists, please enter a new book");
                }
                else
                {

                    Console.Write("    Please enter New Book Name # " + iInsertBook + ": ");
                    string vName = ReadString();

                    Console.Write("    Please enter New Book author # " + iInsertBook + ": ");
                    string vauthor = ReadString();

                    Console.Write("    Please enter New Book year # " + iInsertBook + ": ");
                    int vyear = ReadInteger();

                    Console.Write("    Please enter New Book price # " + iInsertBook + ": ");
                    Double vprice = ReadDouble();

                    Console.Write("    Please enter New Book quantity # " + iInsertBook + ": ");
                    int vquantity = ReadInteger();
                    if (ModifyBook(vbookid, vName, vauthor, vyear, vprice, vquantity))
                    {
                        Console.WriteLine("");
                        Console.WriteLine("    book created");
                        Console.WriteLine("");
                        validateArrayBook = true;
                    }
                    else
                    {
                        Console.WriteLine("");
                        Console.WriteLine("    Error: book was not created");
                    }
                }
            }
        }


        static void ModifyBookById()
        {
            // Implement logic to modify a book by ID
            Console.Write("    Please enter book Id : ");
            vbookid = ReadInteger();

            if (BookExist(vbookid))
            {
                Console.Write("    Actual Book Name : " + books[iDisplayBook].Name);
                Console.Write("    Please enter New Book Name # " + iInsertBook + ": ");
                string vName = ReadString();
                Console.Write("    Actual Book author : " + books[iDisplayBook].Author);
                Console.Write("    Please enter New Book author # " + iInsertBook + ": ");
                string vauthor = ReadString();
                Console.Write("    Actual Book year : " + books[iDisplayBook].YearOfPublication);
                Console.Write("    Please enter New Book year # " + iInsertBook + ": ");
                int vyear = ReadInteger();
                Console.Write("    Actual Book price : " + books[iDisplayBook].UnitPrice);
                Console.Write("    Please enter New Book price # " + iInsertBook + ": ");
                Double vprice = ReadDouble();
                Console.Write("    Actual Book quantity : " + books[iDisplayBook].Quantity);
                Console.Write("    Please enter New Book quantity # " + iInsertBook + ": ");
                int vquantity = ReadInteger();
                if (ModifyBook(vbookid, vName, vauthor, vyear, vprice, vquantity))

                {
                    Console.WriteLine("");
                    Console.WriteLine("   Book modified");
                    Console.WriteLine("");
                    validateArrayBook = true;
                }
                else
                {
                    Console.WriteLine("");
                    Console.WriteLine("    Error: Book was not modified");
                }
            }
            else
            {
                Console.WriteLine("");
                Console.WriteLine("    The Book does not exists, please try again!");
            }
            Console.ReadLine();
        }

        static void SellBook()
        {
        }
        //Function that calculates the total of sales - Array Invoice
        static void DisplayTotalSales()
        {
            double TotalSales = 0;
            for (int i = 0; i < ELEMENTSINVOICE; i++)
            {
                TotalSales = TotalSales + invoices[i].Total;
            }
            Console.WriteLine("The total sales is : $" + TotalSales);

        }

    }

}
