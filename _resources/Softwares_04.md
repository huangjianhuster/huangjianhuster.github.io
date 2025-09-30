---
title: "Quick Gnuplot tricks"
collection: resources
permalink: /gnuplot/
author_profile: true
category: softwares
order: 4
---
# Define your own line style
```
# example
set style line 1 lc rgb "#8b0000" lw 2
set style line 2 lc -1 pt 7 ps 0.9
```

- lc: linecolor; we can use index number (gnuolot has its own default defined colors) or rgb values
- lw: linewidth; a float number
- pt: point type (define marker type)
- ps: point size (define marker size)
we can always use ```test``` command in gnuplot to show the default setups.

```
# example of using style
p sin(x) ls 1 title "sin(x) function", cos(x) ls 2 title "cos(x) function"
```


# Pre-define your style
This function is similar to matplotlib plot style, which we can use `plt.style.use('style.mplstle')` to make a lot of personalized plot settings. There exist a convenient method in gnuplot as well. We first create a `mystyle.gp` file where we define all kinds of general settings (size of the canvas, line styles, grid, borders etc.). Then, we can use `load 'mystyle.gp'` to load those settings. Below is an example:
```
set terminal pngcairo size 800,600 enhanced font 'Arial,12'
set output 'plot.png'

set title "Plot Example" font "Arial, 16"
set xlabel "X Axis" font "Arial, 12"
set ylabel "Y Axis" font "Arial, 12"
set grid
set style line 1 lc rgb "blue" lw 2
set style line 2 lc rgb "red" lw 2 lt 2
set key left top
```



#  Personalize your figure
This part is only based on my personal habits. You can always get the full list and descriptions on the official documentation. 

**legend settings**
```gnuplot
# 1. location
set key bottom left box
set ket at 0.2,-1.8 # exactly control the location at point (0.2, -1.8)

# 2. width
set key width 1

# 3. font
set key font "Arial,14"
```

**border and grid settings**
```
# 1. add grid (and use the predefined linestyle 1)
# Define custom line styles
set style line 1 lt 1 lc rgb "#aaaaaa" lw 1.5  # Major grid: gray, solid, 1.5 width
set style line 2 lt 2 lc rgb "#dddddd" lw 0.5  # Minor grid: light gray, dashed, 0.5 width

# Apply grid with custom styles
set grid xtics ytics linestyle 1
set grid mxtics mytics linestyle 2

#2. remove tics on the right and top
set border 3
set tics nomirror
set border lw 2 # define border line width

```


# Multi-plots Or subplots
```
# Set multiplot mode for 2x2 grid
set multiplot layout 2, 2

# Plot 1: First subplot
plot sin(x) title 'sin(x)'

# Plot 2: Second subplot
plot cos(x) title 'cos(x)'

# Plot 3: Third subplot
plot tan(x) title 'tan(x)' axes x1y1

# Plot 4: Fourth subplot
plot x**2 title 'x^2'

# Disable multiplot mode
unset multiplot

```

# Histogram
```
binwidth = 0.05

bin(x,width)=width*floor(x/width)

# using the col-1 data to plot histogram
p "dist.dat" using (bin($1,binwidth)):(1.0) smooth freq with boxes
```

# Other small tips
**Ignore the first line of your datafile**
```
set key autotitle cloumnhead
```

**output redirect to svg or eps**
```
set terminal svg
set output "example.svg"
```

**sorting**
```
# sorting data based on coloumn 2
# plot x (col2) and y (col3)
plot "< sort nk2 data.dat" u 2:3 w lp
```
