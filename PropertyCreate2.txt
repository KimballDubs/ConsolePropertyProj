using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.SqlClient;

namespace ConsolePropertyProj
{
    class ExternalPropClass
    {
        public static int FinalPropHmy {get; set;} 
        public static string PropCode { get; set; }
        public static string PropName { get; set; }
        public static string PropStreet { get; set; }
        public static string PropCity { get; set; }
        public static string PropState { get; set; }
        public static string PropZipCode { get; set; }

        public static void Menu()
        {
            InputProp();            
            SaveProp();            
        }

        public static void InputProp()
        {
            Console.Clear();
            Console.Write("Enter Property Code: ");
            PropCode = Console.ReadLine();
            Console.Write("Enter Property Name: ");
            PropName = Console.ReadLine();
            Console.Write("Enter Property Street Address: ");
            PropStreet = Console.ReadLine();
            Console.Write("Enter Property City: ");
            PropCity = Console.ReadLine();
            Console.Write("Enter Property State: ");
            PropState = Console.ReadLine();
            Console.Write("Enter Property Zip Code: ");
            PropZipCode = Console.ReadLine();
        }        

        public static void SaveProp()
        {
            char Choice;
            char Choice2;
            SqlConnection con;
            SqlCommand com;
            SqlDataReader dr;
            SqlCommand comSelect;
            SqlDataReader drSelect;                      

            Console.Write("Save?[Y/N]: ");
            Choice = Convert.ToChar(Console.ReadLine());
            Choice = Char.ToUpper(Choice);
            if (Choice == 'Y')
            {
                con = new SqlConnection("Data Source=WIN10-KIMBALL;User ID=sa;Password=Battery1;Initial Catalog=YardiDBLocal;");

                comSelect = new SqlCommand("Select max(hmy) from TestProperty", con);
                con.Open();
                try
                {
                    drSelect = comSelect.ExecuteReader();
                    try
                    {
                        while (drSelect.Read())
                        {
                            FinalPropHmy = (int)drSelect[0];
                            FinalPropHmy++;
                            Console.WriteLine(FinalPropHmy);
                        }
                    }
                    catch (System.InvalidCastException )
                    {
                        FinalPropHmy = 1;                        
                    }
                    catch (Exception e2)
                    {
                        Console.WriteLine(e2.Message);
                    }
                    drSelect.Close();

                    com = new SqlCommand("Insert into TestProperty(HMY,SCODE,Name,Street,City,State,ZipCode) " +
                        "values (@FinalPropHmy,@PropCode,@PropName,@PropStreet,@PropCity,@PropState,@PropZipCode)", con);

                    com.Parameters.AddWithValue("@FinalPropHmy", FinalPropHmy);
                    com.Parameters.AddWithValue("@PropCode", PropCode);
                    com.Parameters.AddWithValue("@PropName", PropName);
                    com.Parameters.AddWithValue("@PropStreet", PropStreet);
                    com.Parameters.AddWithValue("@PropCity", PropCity);
                    com.Parameters.AddWithValue("@PropState", PropState);
                    com.Parameters.AddWithValue("@PropZipCode", PropZipCode);

                    dr = com.ExecuteReader();
                    dr.Read();

                    DisplayProp();

                    Console.Write("\n\nInput another record?[Y/N]: ");
                    Choice2 = Convert.ToChar(Console.ReadLine());
                    Choice2 = Char.ToUpper(Choice2);
                    if (Choice2 == 'Y')
                    {
                        Menu();
                    }
                    else
                    {
                        dr.Close();
                        con.Close();
                        return;
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Save Fail!!! Error: {0}", e.Message);
                }
            }
            else
            {
                Menu();
            }
        }

        public static void DisplayProp()
        {
            Console.Clear();            
            Console.WriteLine("Property Hmy: {0}", FinalPropHmy);
            Console.WriteLine("Property Code: {0}", PropCode);
            Console.WriteLine("Property Name: {0}", PropName);
            Console.WriteLine("Property Street: {0}", PropStreet);
            Console.WriteLine("Property City: {0}", PropCity);
            Console.WriteLine("Property State: {0}", PropState);
            Console.WriteLine("Property Zip Code: {0}", PropZipCode);
            Console.WriteLine("Above property is Successfully Saved!!!!");
        }
    }
}
