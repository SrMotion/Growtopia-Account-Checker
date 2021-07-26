# Growtopia Account Checker Libary

[![Discord](https://img.shields.io/discord/863569897754066964?label=discord)](https://discord.gg/fRwrV53pux) 

## Example Console App: 

```csharp
using AccCheckerLib;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace AccountChecker
{
    class Program
    {
        public static List<string> ItemDB = new List<string>();
        static void SetupItemDatabase()
        {
            if (Properties.Resources.Items != null)
            {
                //by SrMotion#6900
                StringReader sr = new StringReader(Properties.Resources.Items);
                string items = String.Empty;
                int item = 0;
                while ((items = sr.ReadLine()) != null)
                {
                    ItemDB.Add(items);
                    item++;
                }
                Console.WriteLine("[+] Item database set with {0} items!", item);
            }
            else
            {
                Console.WriteLine("[+] Items are null!");
            }
        }
        private static void Status_valueChanged(object sender, EventArgs e)
        {
            string txt = (sender as StringEvt).Text;
            Console.WriteLine(txt); //Account Checking status
        }
        private static void OnCheckingDone_CheckingDone(object sender, EventArgs e)
        {
            var evt = sender as CheckingEvent;
            if (evt != null)
            {
                var value = evt.UserInfo;
                if (value.Success)
                {
                    Console.WriteLine("World Lock Balance: {0}", value.WorldLock_Balance);
                    Console.WriteLine("Gems: {0}", value.GemAmount);
                    Console.WriteLine("Supporter: {0}", value.Supporter);
                    Console.WriteLine("Hours Played: {0}", value.Hours_Played);
                    Console.WriteLine("Player Level: {0}", value.PlayerLevel);

                    Console.WriteLine("Inventory Items: ");
                    foreach (PlayerInvItems items in value.InventoryItems)
                    {
                        Console.WriteLine("Item Name: {3} ItemID: {0} Count: {1} Equipped: {2}", items.itemID, items.count, items.equipped,ItemDB[items.itemID]);
                    }
                    Console.WriteLine("Worlds: ");
                    value.Worlds.ForEach(Console.WriteLine);
                    Console.WriteLine("Checking is done!");
                }
                else
                {
                    Console.WriteLine("Checking failed");
                }
            }
        }
        public static string GenerateMac()
        {
            var random = new Random();
            var buffer = new byte[6];
            random.NextBytes(buffer);
            var result = String.Concat(buffer.Select(x => string.Format("{0}", x.ToString("X2"))).ToArray());
            return result;
        }
        static void Main(string[] args)
        {
            SetupItemDatabase();
            var acccheckerInstace = new AccCheckerLib.AccChecker("GrowID", "Password", "213.179.209.168", "17201", "3.66");
            acccheckerInstace.initialize(GenerateMac());
            acccheckerInstace.status.valueChanged += Status_valueChanged;
            acccheckerInstace.OnCheckingDone.CheckingDone += OnCheckingDone_CheckingDone;
            acccheckerInstace.ConnectCurrent();
            Console.ReadKey();
        }
    }
}
```
