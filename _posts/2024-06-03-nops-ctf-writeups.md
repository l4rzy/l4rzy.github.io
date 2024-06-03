---
layout: post
title: "[CTF] N0PS CTF writeups"
categories: ctf re forensics
---

## Reverse Me

```sh
> file img.jpg
img.jpg: JPEG image data
```

The file looks like a JPEG, but it does not show anything in an image viewer, let's have a closer look.
```sh
> xxd img.jpg
00000000: ffd8 ffe0 0000 0000 0000 0000 0000 0000  ................
00000010: 0000 0000 0000 0001 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 010a 0000 0000 0000 303b  ..............0;
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000040: 0000 0003 0000 0001 0000 0000 0000 0001  ................
00000050: 0000 0000 0000 0001 0000 0000 0000 0000  ................
00000060: 0000 0000 0000 002b 0000 0000 0000 3010  .......+......0.
00000070: 0000 0000 0000 0000 0000 0000 0000 0030  ...............0
...
```
The file starts with `ffd8 ffe0`, which is a signature for [Raw JPEG](https://en.wikipedia.org/wiki/List_of_file_signatures), so what could be wrong?
![raw jpeg](/assets/images/nopsctf/jpeg.png)

```sh
...
00003800: 0000 0000 0000 0318 0000 0000 0000 0318  ................
00003810: 0000 0004 0000 0003 0000 0000 0000 0008  ................
00003820: 0000 0000 0000 02d8 0000 0000 0000 02d8  ................
00003830: 0000 0000 0000 0040 0000 0000 0000 0040  .......@.......@
00003840: 0000 0000 0000 0040 0000 0004 0000 0006  .......@........
00003850: 001c 001d 0040 000d 0038 0040 0000 0000  .....@...8.@....
00003860: 0000 0000 0000 3148 0000 0000 0000 0040  ......1H.......@
00003870: 0000 0000 0000 1310 0000 0001 003e 0003  .............>..
00003880: 0000 0000 0000 0000 0001 0102 464c 457f  ............FLE.
```
Scrolling to the end of the file, there is something odd. That's the ELF binary signature in reverse. Let's reverse the file back. And voila!

```python
input_file = "img.jpg"
output_file = "img.elf"

with open(input_file, 'rb') as f:
    data = f.read()

reversed_data = data[::-1]

with open(output_file, 'wb') as f:
    f.write(reversed_data)
```

```
> python reverse.py

> file img.elf
img.elf: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=ae64a94832a94702644e170ebf1177740605cb34, for GNU/Linux 3.2.0, stripped
```

Now, let's open the file in IDA.
![ida](/assets/images/nopsctf/ida.png)

The program reads 4 arguments and calls `sub_1460` to do some check on them. The check is quite simple.
```c
__int64 __fastcall sub_1460(int a1, int a2, int a3, int a4)
{
  unsigned int v4; // r8d

  v4 = 0;
  if ( 3 * a4 + a3 + 4 * a2 - 10 * a1 != 28 )
    return 0LL;
  if ( 9 * a2 - 8 * a1 + 6 * a3 - 2 * a4 == 72 && a4 + -3 * a2 - 2 * a1 - 8 * a3 == 29 )
    LOBYTE(v4) = a3 + 5 * a1 + 7 * a2 - 6 * a4 == 88;
  return v4;
}
```

Let's find the 4 numbers that satisfy the check with z3.
```python
import z3
from z3 import Solver, Real

def solve_range_equation():
    # Create a solver
    solver = Solver()

    # Define variables
    a1 = Real('a1')
    a2 = Real('a2')
    a3 = Real('a3')
    a4 = Real('a4')

    # Add equation to the solver
    solver.add(3 * a4 + a3 + 4 * a2 - 10 * a1 == 28)
    solver.add(9 * a2 - 8 * a1 + 6 * a3 - 2 * a4 == 72)
    solver.add(a4 + -3 * a2 - 2 * a1 - 8 * a3 == 29)
    solver.add(a3 + 5 * a1 + 7 * a2 - 6 * a4 == 88)

    # Check satisfiability and get the solution
    if solver.check() == z3.sat:
        model = solver.model()
        print("Solution:", model[a1], model[a2], model[a3], model[a4])
    else:
        print("No solution found.")

if __name__ == "__main__":
    solve_range_equation()
```

```
> python solve.py
Solution: -3 8 -7 -9
```

And finally solve the challenge:
```
> chmod +x img.elf
> ./img.elf -3 8 -7 -9
N0PS{r1CKUNr0111N6}
```

## HID

The challenge provides a pcapng file (Packet capture). Let's open it in Wireshark.
![wireshark](/assets/images/nopsctf/wireshark.png)

Since there are packets that are irrelevant to HID, let's filter them out with `usbhid.data`

Now we're left with all the HID packets. After scrolling up and down, we can see clearly that there are two HID devices: a keyboard, and a pointing device. The thing is, there is only one packet from the keyboard, all other packets are from the pointing device (supposedly a mouse). Let's analyze them.

![keyboard](/assets/images/nopsctf/keyboard.png)

![mouse](/assets/images/nopsctf/mouse.png)

At this point, we can forget the keyboard and focus on the mouse, since the mouse has information about its movements (x axis and y axis). Let's filter out the keyboard and export the result to another file for further processing.

![filtered](/assets/images/nopsctf/filtered.png)

Since we know the offsets in Wireshark, we will use Python to extract all the movements, and visualize it.

```python
import matplotlib.pyplot as plt
from scapy.all import *

def draw_plot(movements):
    # Initialize coordinates with the root point
    x_coords = [0]
    y_coords = [0]

    # Accumulate movements to generate coordinates
    current_x = 0
    current_y = 0
    for movement in movements:
        current_x += movement[0]
        current_y += movement[1]
        x_coords.append(current_x)
        y_coords.append(current_y)

    # Plot the points
    plt.plot(x_coords, y_coords, marker='o')

    # Connect all points with lines
    plt.plot(x_coords, y_coords, linestyle='-', color='b')

    # Add labels and title
    plt.xlabel('X Coordinate')
    plt.ylabel('Y Coordinate')
    plt.title('Plot of Movements')

    # Show the plot
    plt.grid(True)
    plt.show()

if __name__ == "__main__":
    # Set the input pcap file path
    input_file = "filtered.pcapng"
    packets = rdpcap(input_file)

    movements = []
    for p in packets:
        xb = p.load[66:68]
        yb = p.load[68:70]

        print(xb, yb)

        x = struct.unpack('<h', xb)[0]
        y = struct.unpack('<h', yb)[0]

        movements.append((x,y))

    # Draw mouse movement
    draw_plot(movements)

```
![movements](/assets/images/nopsctf/movements.png)

It doesn't look quite right, does it? Because in computer graphics, the 2D Cartesian Coordinate System is horizontally flipped, so we have to flip the image upside down. And here's the result.

![flipped](/assets/images/nopsctf/flipped.png)

It took me a while to guess the flag due to the ugly mousewriting. `N0PS{m0Us3_dR4w1Ng}`