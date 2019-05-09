# Slob
Objective:
	The objective of our project is to design algorithm for the SLOB allocations i.e. first-fit algorithm, best-fit algorithm and worst-fit algorithm for fixed size partitioning scheme.
First-fit algorithm:

 Best-fit algorithm:
 

Worst-fit algorithm:

Code:
First-fit Code:
//
	//First Fit Algorithm
	//
	

	double internal_frag = 0, external_frag = 0;

	int partitions[] = { 4, 16, 22, 16, 64, 14, 22, 32, 2, 64 };
	bool allocation_process[10] = { 0,0,0,0,0,0,0,0,0,0 };
	std::string a = "P-00";

	for (int i = 0; i < 10; i++)
	{
		bool allocated = false;
		for (int j = 0; j < 10; j++)
		{
			if (size_p[i] > 0 && partitions[j] >= size_p[i] && !allocation_process[j])
			{
				//Converting process name to string
				if (i < 9)
				{
					a[3] = '0' + (i + 1);
					a[2] = '0';
				}
				else
				{
					a[2] = '1';
					a[3] = '0';
				}
				String ^process_name = gcnew String(a.c_str());
				Process_Name_First_Fit(j, process_name);
				allocated = true;
				allocation_process[j] = 1;
				internal_frag += partitions[j] - size_p[i];
				First_Panel(j);
				break;
			}
		}
		if (!allocated && size_p[i] != 0)
		{
			external_frag += size_p[i];
			First_Wait(i);
		}
	}
	
	//Internal Fragmentation Display
	label88->Show();
	label94->Text = System::Convert::ToString(internal_frag);
	label94->Show();
	label100->Show();

	/*End of First Fit*/
Best-fit Code:
/*
	* Best Fit Algorithm
	*/
	
	internal_frag = 0;
	external_frag = 0;
	memset(allocation_process, 0, sizeof(bool) * 10);
	a = "P-00";

	for (int i = 0; i < 10; i++)
	{
		int allocation_unit = -1;
		double min_diff = 9999;
		//Converting process name to string
		if (i < 9)
		{
			a[3] = '0' + (i + 1);
			a[2] = '0';
		}
		else
		{
			a[2] = '1';
			a[3] = '0';
		}
		String ^process_name = gcnew String(a.c_str());
		for (int j = 0; j < 10; j++)
		{
			if (!allocation_process[j] && size_p[i] <= partitions[j] && (min_diff > (partitions[j] - size_p[i])) && size_p[i] > 0)
			{
				min_diff = partitions[j] - size_p[i];
				allocation_unit = j;
			}
		}
		if (allocation_unit > -1)	//color code the selected panel
		{
			internal_frag += partitions[allocation_unit] - size_p[i];
			allocation_process[allocation_unit] = true;
			Process_Name_Best_Fit(allocation_unit, process_name);
			Best_Panel(allocation_unit);
		}
		else if (size_p[i] != 0)	//make the process wait
		{
			external_frag += size_p[i];
			Best_Wait(i);
		}
	}

	//Internal Fragmentation Display (Best Fit)
	label90->Show();
	label96->Text = System::Convert::ToString(internal_frag);
	label96->Show();
	label102->Show();

	/*
	* End of Best Fit Algorithm
	*/
Worst-fit Code:
	/*
	* Worst Fit Algorithm
	*/

	internal_frag = 0;
	external_frag = 0;
	memset(allocation_process, 0, sizeof(bool) * 10);
	a = "P-00";

	for (int i = 0; i < 10; i++)
	{
		int allocation_unit = -1;
		double max_diff = -1;
		//Converting process name to string
		if (i < 9)
		{
			a[3] = '0' + (i + 1);
			a[2] = '0';
		}
		else
		{
			a[2] = '1';
			a[3] = '0';
		}
		String ^process_name = gcnew String(a.c_str());
		for (int j = 0; j < 10; j++)
		{
			if (!allocation_process[j] && size_p[i] <= partitions[j] && (max_diff < (partitions[j] - size_p[i])) && size_p[i] > 0)
			{
				max_diff = partitions[j] - size_p[i];
				allocation_unit = j;
			}
		}
		if (allocation_unit > -1)	//color code the selected panel
		{
			internal_frag += partitions[allocation_unit] - size_p[i];
			allocation_process[allocation_unit] = true;
			Process_Name_Worst_Fit(allocation_unit, process_name);
			Worst_Panel(allocation_unit);
		}
		else if (size_p[i] != 0)	//make the process wait
		{
			external_frag += size_p[i];
			Worst_Wait(i);
		}
	}

	//Internal Fragmentation Display (Worst Fit)
	label92->Show();
	label98->Text = System::Convert::ToString(internal_frag);
	label98->Show();
	label104->Show();

	/*
	* End of Worst Fit Algorithm
	*/

Kernel Implementation Best Fit Code:
#include<linux/kernel.h>

asmlinkage long sys_slob(void)
{
	int partitions[] = {4,16,22,16,64,14,22,32,64,2};
	int process_size[] = {51, 12, 42, 5, 7, 16, 1, 64};

	printk("Memory of size 256MB\n");
	for (int i=0; i<10; i++)
		printk("Partition %d size: %d MB\n", i, partitions[i]);

	printk("\n\nProcesses to be allocated:\n");
	for (int i=0; i<8; i++)
		printk("Process %d size: %d MB\n", i, process_size[i]);

	printk("\nAllocating processes...\n");
	int allocated[] = {0,0,0,0,0,0,0,0,0,0};
	for (int i=0; i<8; i++)
	{
		int temp_allocation = -1;
		int min_diff = 99999;
		for (int j=0; j<10; j++)
		{
			if (allocated[j] == 0 && min_diff > (partitions[j] - process_size[i]) && partitions[j] >= process_size[i])
			{
				min_diff = partitions[j] - process_size[i];
				temp_allocation = j;
			}
		}
		if (temp_allocation != -1)
		{
			allocated[temp_allocation] = 1;
			printk("[INFO]Process %d allocated in slot %d(%d MB)\n", i,temp_allocation,partitions[temp_allocation]);
		}
		else
			printk("[INFO]Process %d waiting...[No Free Slots Available]\n", i);
	}


	return 0;
}
Result:
		The program allocates the given size of processes according to the partition available with respect to the given algorithm. For example in first fit the program looks in the available partitions for the first appropriate size i.e. partition size >= process size.
Conclusion:
		Through many runs of code with different size of processes, it is clear that best fit produces the best result i.e. least internal fragmentation is found in best fit, while its counterpart worst fit produces the worst result i.e. highest internal fragmentation.
