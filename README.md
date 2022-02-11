# membound
Runtime testing of memory boundaries when accessing arrays or dereferencing pointers.
Inspired by the worst bug I've ever encountered in 16 years of coding in C.
Has saved me many horrific hours of diagnosis by catching problems early.

If you are performing calcuations vunerable to human error on array indexes or pointer arithmetic,
you can wrap the string.h functions (memset memcpy memmove strcat strcpy etc..) with _mb wrapper functions
which also accept a *membound_struct with information about the memory boundaries of the destination.

Just include membound.c and membound.h in your project and define your violation handler at the top of membound.c

MEMBOUND_OF() macro can be used to assign a membound struct from a pointer to the type.

	eg.
	{
		struct membound_struct 	fred_mb;
		fred_t				 	*fred;

		fred 	= malloc(sizeof(fred_t));
		fred_mb	= MEMBOUND_OF(fred);

		//safely write somewhere into fred
		memcpy_mb(&fred_mb, etc..
	}

Being a macro, it can also be used on arrays without pointer decay

	{
		char   name[MAXLEN_NAME+1];
		struct membound_struct	name_mb;

		name_mb	= MEMBOUND_OF(&name);

		//safely write somewhere into name
		memcpy_mb(&name_mb, etc..
	}

When dereferencing a pointer, it may be wrapped in MBTST for testing

	{
		int myints[NUMBER_OF_OINT];
		int *myints_ptr;
		struct membound_struct	myints_mb;

		myints_mb = MEMBOUND_OF(&myints);

		myints_ptr = some_dangerous_calcuation(myints, floopdeboop, gobbleboopdegop);
		
		//instead of 
		// *myint_ptr = 13;
		//use 
    
		*MBTST(myints_mb, myint_ptr) = 13;
	}
