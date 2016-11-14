MEF in .NET Core
----------------------
1. A .NET Core Console Application is created.(Simple Calculator)
2. A .NET Core Class Library is created.(Base Operation)
3. A .NET Core Class Library is created.(Extended Operation)

	Structure
-------------------------------------------------------------------------------------------
Simple Calculator Application
BaseOperation: 
	IOperation <<interface>>:
		int Operate(int,int)
	IOperationData <<class>>:
		// (metadata, it must be concrete class not an interface because of .NET Core)
		char Symbol // must be public and write access because of .NET Core
ExtendedOperation:
	Add : BaseOperation.IOperation
		int Operate(int,int)
	Subtract: BaseOperation.IOperation
		int Operate(int,int)

Explanation: Simple Calculator console application has only the reference of BaseOperation library and Extended Operation will
	     be loaded dynamically in run time.
--------------------------------------------------------------------------------------------

4. Add an interface to SimpleCalculator project : ICalculator
	ICalculator <<interface>>:
		string Calculate(string)
5. Add a property in Program.cs:

	[Import]
        public ICalculator calculator { get; set; }
	
	*In order to use "Import" attribute, "Microsoft.Compostion" package must be installed. (via NuGet Package Manager) and add using directives.
	*In order to resolve incompability problem, "portable-net45+win8" framework must be added to import part of framework in project.json file.

6. Add an class which implement ICalculator interface:
	[Export(typeof(ICalculator))]
	MySimpleCalculator: ICalculator:
		string Calculate(string)

7. The key point here, Import keyword has no constructor with type of "Type" parameter as in .net 4.5. However the default contract is typeof(property_type). 
   Export has necessary constructor and it takes typeof(property_type) as contract.

8. BaseOperation and Extended Operation library are created as shown in structure. In an example Add class:

	[Export(typeof(BaseOperations.IOperation))]
    	[ExportMetadata("Symbol", '+')]
	public class Add : BaseOperations.IOperation
	{
		public int Operate(int left, int right)
	        {
	            return left + right;
	        }
	}

9. BaseOperation and ExtendedOperation library must have Microsoft.Composition package. Do not forget important points(*) in bullet 5.

10. MySimpleCalculator class an property for operation:

	[ImportMany]
        public IEnumerable<Lazy<BaseOperation.IOperation, BaseOperation.IOperationData>> operations { get; set; }

11. Add ContainerConfigurationExtension class. It  have some methods that search dlls in indicated path and load. Moreover, "System.Runtime.Loader": "4.0.0" 
    must be added to project.json file as dependencies.	

12. In last part, in constrator of Program class, dlls must be loaded.

13. Other details are in source code on github. Please check it.








