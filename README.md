Download Link: https://assignmentchef.com/product/solved-cpsc2310-lab-12-assembly-code
<br>
The goal of this lab is to introduce assembly code; the low-level languages used to interact with computer hardware. We will be looking at and manipulating x86, and also creating a small program using ARM.  You will be answering a few questions in the provided <strong>Lab12Questions.txt</strong> file as we progress through the lab, and submitting that .txt file along with all of the other files you create today.

<strong>Part 1 – x86 intro</strong>

Our lab machines run on an x86 instruction set architecture (ISA). An ISA is essentially a description of what can run on a computer’s hardware, and is the boundary between hardware and software. Compilers turn code (C, C++, Java, etc) into something the machine can actually understand using the ISA. We can use <strong>gcc</strong> to generate x86 code so we can view it directly (you’ve done this in lecture).

In your Lab6 directory, create a new file <strong>driver.c</strong> and write this simple loop:

#include &lt;stdio.h&gt;

int main(){




int min = 0;

int max = 10;

for(int i = 0; i &lt;= max; i++){

printf(“%d “, i);

}




printf(“
”);

return 0;

}







Go ahead and compile and run the program normally to make sure it behaves as expected:

<strong>gcc -Wall driver.c</strong>




Now, compile again using this command:

<strong>gcc -S -fverbose-asm driver.c</strong>

<strong> </strong>

The -S flag generates an x86 file, and the -fverbose-asm flag adds some comments which could be helpful.  Go ahead and take a quick glance at the <strong>driver.s </strong>file that was generated and see how 1 small for loop program was translated into something with roughly 4 times as many lines.  This is why we abstract coding into higher level language like C.  We can compile the <strong>driver.s </strong>file using <strong>gcc </strong>exactly as we did <strong>driver.c.  </strong>Do that now, and run the executable to see the results are the same.




Now, we’ll take a closer look at <strong>driver.s</strong>

You do not need to know all the specifics the x86 instruction set for this lab, and we won’t cover it in-depth, but we will get to some basic understanding of what’s happening. Some basics: the commands you see at the start of each line (push, mov, sub, etc.) are called mnemonics. They’re usually followed by a letter (q, l, w, b).  At a base level, the mnemonic is the description for what we’re doing to some variable, the letter represents the size of the variable.  The <strong>$</strong> signals literal values, while <strong>% </strong>signals a register. If you see the following lines:

movl  $0, -8(%rbp)   #, min

movl  $10, -4(%rbp)  #, max




The <strong>mov </strong>mnemonic places a value into a memory address. Those 2 lines are where we assign the value <strong>0</strong> to min, and the value <strong>10</strong> to max.  This is where that -fverbose-asm flag comes in handy by commenting the variable names to the right of the line. Otherwise, we would’ve had to work back to understand the memory locations being referred to by <strong>-8(%rbp) </strong>and <strong>-4(%rbp)</strong>. As it stands, it’s sufficient to know that <strong>%rbp</strong> is a pointer to the base of the stack, and <strong>%rsp</strong> points to the top of the stack. The first few lines in <strong>main:</strong> set up the stack to hold enough space for the ints we use, and we can tell that max, min, and i are respectively stored at memory locations 8, 4, and 12 bytes offset from the base of the stack.




<strong>Part 2- x86 manipulation</strong>




Now let’s mess with the driver.s file a little and see what happens. Go ahead and try changing the values from the 2 lines above to other numbers and see what happens. Use your results to fill in the table for question 2




Change the values back to 0 and 10, and notice how the end of <strong>main:</strong> has the <strong>jmp </strong>command. This sends the program counter to the label specified. In this case, .<strong>L2</strong>

It’s similar to a function call. When you call a function, the code there will be executed next.

See if you can follow along with these brief descriptions of <strong>.L2 </strong>and <strong>.L3</strong>




<strong>.L2</strong>

Places <strong>i</strong>’s value in a temp register, and the compares that value to <strong>max</strong>

<strong>jle</strong> – <strong>J</strong>ump if <strong>L</strong>ess than or <strong>E</strong>qual to

Any time a comparison is done, the result is stored in a register which is looked at whenever a conditional jump command is used.

The rest in this section can be ignored




<strong>.L3</strong>

Puts <strong>i</strong>’s value in a temp register

Uses the temp register to connect to the start of a stream operation <strong>%esi</strong>

Uses the LCO section at the top of the file to call printf with the specified string.

Increments <strong>i</strong>




<strong>You should now be able to answer questions 1 – 5</strong>




<strong>Part 3 – ARM intro</strong>We’re going to dive right into another assembly language called ARM. If you look at the provided <strong>main.c</strong> file, you’ll see a basic main function which creates several arrays and then prints out the result when those arrays are passed into a function called absVal() which is not defined yet. This function returns the absolute value of the all integers in an array added together. Your job is to write the absVal function in ARM (with some help, of course).




This is the standard idea of the function in C:




int absVal(int* arr, int length){

int total = 0;

for(int i = 0; i &lt; length; i++){

if(arr[i] &lt; 0){

total -= arr[i];

}else{

Total += arr[i];

}

}

return total;

}










Go ahead and open up the <strong>absVal.s</strong> file. The basic outline for the function is already in place. We can now use this outline to cover some of the ARM fundamentals. Specifically, how functions work in ARM. When you call a function, the parameters are stored on registers, which are designated by the letter R and then a number, starting at R0. Since we have two parameters, those will be passed in R0 and R1. Meanwhile, when our function ends and we end up back in main, the computer will look for the return value on R0.  This means the last step of the absVal function will be to move the absolute value to the R0 register.  We don’t do that at the beginning because R0 is where the array address is stored, and we don’t want to overwrite it.




<strong>A key concept in ARM is keeping track of which registers are storing which information. That’s why it’s good to define the registers you’re using in a comment at the beginning of a file such has been done for you in absVal.s</strong>

<strong> </strong>

I’ve already set up the basic structure of your <strong>absVal.s</strong> file.




<strong>Labels</strong>: any word followed by a colon is a label. It defines a section of code, and is not in itself a command.  That means you can have as many labels as you want, and they don’t interfere with the code at all. In the following example, the computer will execute instruction 1 and then instruction 2

Main:

Boo:

instruction 1

lessthan:

loop:

go:

label:

instruction 2




The point of labels is to tell the program where to go whenever branches are used. Branches in ARM are the equivalent of jumping in x86.  Branches/jumps are how we deal with conditionals in assembly languages. If you look back to the C example above, you’ll see we have 3 conditionals: 1 for the for loop, the if, and the else. This means we have different sections of code that may or may not be executed; we’ll divide those sections into different labels with ARM




I’ve already set up labels in <strong>absVal.c </strong>

The <strong>loop</strong> label is where we set up the basic loop execution

We’ll branch to <strong>add</strong> if the current int (arr[i]) is positive, and to <strong>subtract</strong> if it’s negative

<strong>increment </strong>will be used to increment i and then branch immediately back to <strong>loop</strong>

<strong>done </strong>sets the return value




<strong><u>Things that were done for you</u></strong>

Before we get into what you need to do, let’s take a look at what has already been completed.




<strong>loop</strong>

In loop, r2 and r1 are compared, and then we bge (<strong>b</strong>ranch if <strong>g</strong>reater than or <strong>e</strong>qual to) to <strong>done</strong>

This accomplishes the i &lt; length conditional in the for loop above.




If i is less than the length (ie, not greater than or equal to), then we continue on in the <strong>loop</strong> code.

The description for the next two lines is in the comments next to them. You need to look up the <strong>lsl </strong> and <strong>ldr</strong> commands online and answer question 6 to describe what those commands mean.




<strong>Increment</strong>

This section has been completed for you to demonstrate how mnemonics and opcodes work in ARM

add     r2, r2, #1 is the equivalent of r2 = r2 + 1

Basic math operations in ARM follow this same format (hint to how <strong>lsl</strong> works)

Remember how in x86 the <strong>$</strong> showed we were using the value of the following number? In ARM, the <strong>#</strong> represents the same thing.




bal     loop




this is a branch command, because it starts with <strong>b</strong>, but <strong>al</strong> means always. So at the end of the increment section, the code will then always go back to <strong>loop</strong>







<strong>done</strong>

done moves (<strong>mov</strong>) the total from where we’ve been storing it on r5 to r0

pops some stuff off the stack. If you notice, at the beginning of the function, we pushed several values onto the stack: r3, r4, r5, and lr

We stored r3, r4 and r5 on the stack because it’s highly possible that main() was using those registers to store something, but we need those registers for our functions. When the program returns to main, we want to preserve whatever values were already in those registers, so that main can function correctly. So we push them on the stack, then pop them back in the registers when we’re done.




Look up the meaning of lr and pc to give your best shot at answering question 7.




<strong>Part 4 – ARM coding</strong>

<strong> </strong>

Step 1: initializing variables

You should initialize all the registers you’ll be using to zero (besides the parameters)

One has been done for you; use it as an example for the others.




Step 2: if/else

I’ve already written the comparison opcode for the if/else in the c function above. Use branching to go to the appropriate label. Here’s a list of possible branching suffixes:

al      always (just ‘b’ is also branch always)

eq      equal (zero)

ne      nonequal (nonzero)

cs      carry set (same as HS)

cc      carry clear (same as LO)

mi      minus

pl      positive or zero

vs      overflow set

vc      overflow clear

hs      unsigned higher or same

lo      unsigned lower

hi      unsigned higher

ls      unsigned lower or same

ge      signed greater than or equal

lt      signed less than

gt      signed greater than

le      signed less than or equal




Step 3: Add and subtract

Fill in what needs to happen in the body of the if/else statement, and then branch to the next step in the code. Look up commands in ARM if you’re not sure. Use the other examples in the given code.










<strong>Compiling ARM</strong>

To compile your code, use the following command:

arm-linux-gnueabi-gcc-5 absVal.s main.c –static