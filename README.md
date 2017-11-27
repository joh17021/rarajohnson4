# rarajohnson4
madlib Project
#include <iostream>
#include <fstream>
#include <string.h>
#include <stdio.h>
#define ERROR 1
#define DEBUG false

using namespace std;

//Function prototypes
void getFileName(char []);
void readFile(char[], char[]);
void printOutput(char[]);

int main()
{  
   bool keepGoing = true;
   char userInput[256];
   while (keepGoing)
   {
	   //Read in file from the user
	   char fileName[256];
	   char output[256];
	   getFileName(fileName);
	   //Parse file
	   readFile(fileName, output);
	   //Print resulting madlib
	   printOutput(output);
	   cout << "Do you want to play again? (y/n)\n";
	   cin.getline(userInput, 256);
	   if (userInput[0] == 'y' || userInput[0] == 'Y')
	   {
	      keepGoing = true;
	   }
	   else
	   {
	      keepGoing = false;
	   }
   }
   return 0;
}

//Get fileName from user
//Parameter: fileName: The variable that the fileName will be saved to
void getFileName(char fileName[])
{
   if(DEBUG) cout << "Starting the program" << endl;
   cout << "Please enter the filename of the Mad Lib: ";
   cin >> fileName;
   if(DEBUG) 
      cout << "You opened the file: " << fileName << endl;
}

//Opens file and parse contents
//Replaces words in <> with user input
//Param fileName: file to parse input
//Param output: MadLib with replaced words
void readFile(char fileName[], char output[])
{
   bool seen_chevron = false;
   int numChar = 0;
   char letter;
   char madlibPrompt[256];
   char userInput[256];

   ifstream fin(fileName);
   if (fin.fail())
   {
      cout << "error reading file: " << fileName << endl;
      throw ERROR;
   }

   //look at eah char && if letter is < then save it in the madlibprompt
   //until you see a >. after the '>', prompt the user for that variable
   //save user input to output string
   while (numChar < 256  && fin >> noskipws >> letter)  
   {
      if(DEBUG) cout << "looking at char " << letter << endl;

      //start builing the madlib prompt
      if (seen_chevron)
      {
         //prompt the usere with the madldib prompt and save inupt to output string
         if (letter == '>')
	 {
	    bool outputstring = false;
	    seen_chevron = false;
	    if (strlen(madlibPrompt) == 1)
	    {
	       
	      switch (madlibPrompt[0])
	      {
		 case '#':
		    strncat(output, "\n", 1);
		 break;
		 case '{':
		    strncat(output, " ", 1);
		    strncat(output, "\"", 1);
		 break;
		 case '}': 
		    strncat(output, "\"", 1);
		    strncat(output, " ", 1);
		 break;
		 case '[':
		    strncat(output, " ", 1);
		    strncat(output, "\'", 1);
		 break;
		 case ']':
		    strncat(output, "\'", 1);
		    strncat(output, " ", 1);
		 break;
		 default:
		       outputstring = true;
		       //Assuming that one character prompts will always be special
		 break;
	      }        
	    }
	    else
	    {
	    	outputstring = true;
            }
	    if (outputstring)
	    {
		    if (DEBUG) cout << "Seen right chevron" << endl;
		    madlibPrompt[0] = toupper(madlibPrompt[0]); 
		    int newSize;
		    for (int i = 1; i < 256; ++i)
		    {
                       if (madlibPrompt[i] == '_'){
		          madlibPrompt[i] = ' ';
			}
                    }
		    cout << "\t" << madlibPrompt << ": ";
		    cin >> userInput;
		    //add userinput to output
		    strncat(output, userInput, strlen(userInput));

	  }
	    memset(madlibPrompt, 0, 256);
	    memset(userInput, 0, 256);
	 }

	 else
         {
	    strncat(madlibPrompt, &letter, 1);
	 }
      }
      else // not seen chevron
      {
         if (letter == '<')
	 {
	    //Assuming there will not be nested chevrons
	    if (DEBUG) cout << "Seen left chevron" << endl;
	    seen_chevron = true;
	 }
	 //save character to output
	 else
	 {
	    strncat(output, &letter, 1);
	 }
      }
   }
	fin.close();
}

void printOutput(char output[])
{
   cout << output;
}
