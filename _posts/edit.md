
# Debugging

For the past 2 weeks, I've been debugging the json module. I hooked up the json module into the code base hieracry which means I made one of the classes that was extending GObject into extending my class, the json module. Testing the test case we have crashes the program.

In the beginning I was having quite the difficulty trying to use gdb and coredumpctl.

./autogen.sh --enable-debug
make 

and 

./configure --enable-debug
make

and I built vala using GNOME builder and it still didn't enable debug symbols to show the functions I needed, the whole point was to see which function fails.

Lorenz, my mentor, helped me alot on this issue. How we were able to fix this was. 


# GUADEC 
