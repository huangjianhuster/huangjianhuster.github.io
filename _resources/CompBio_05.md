---
title: "Important VMD tricks"
collection: resources
category: computational biophysics
order: 5
---

In this article, I want to include some VMD tricks based on my personal experience. VMD provides an official documentation, which can be found here:  [VMD-documentation](https://www.ks.uiuc.edu/Research/vmd/current/docs.html). Readers are suggested to refer to the [VMD User's Guide](https://www.ks.uiuc.edu/Research/vmd/current/ug/ug.html) for more detailed information.

# 1. Customize VMD default settings
We can use a vmdrc file to control VMD initialization and related settings, including but not limited to: window location on the screen, color settings (background etc), display settings, views and so on. Below is a tutorial to show how to customize your own vmdrc.
From the `VMD Main` menu, go to `Extension`, then `VMD Preferences`. In the pop out `VMD Preference Panel` window, make changes to your preference. After you are done with the changes, click on `Write Settings to VMDRC`.
Here, I provide my personal settings:
- Display/Projection Type: Orthographic
- Display, query window position (arrange windows to your preferred positions)
- Color or Display: background color
- Render mode: GLSL
- Material: AOChalky
Of note, you may need to go to the generated .vmdrc file and find `menu vmdprefs` and turn it off.


# 2. Define your own color and material in vmdrc
We can define our own color and materials inside the `after idle` block of vmdrc.
```tcl
...
...
after idle {

	# Define your own color
	if { 1 } {
		color salmon 1 0.4745 0.4235
	}

	# Define your own material
	if { 1 } {
	    # define a new, very transparent material 'FadingSurf'
        # specular shininess mirror opacity outline outlinewidth transmode
        material add FadingSurf
        material change ambient   FadingSurf 0.20
        material change diffuse   FadingSurf 0.61
        material change specular  FadingSurf 0.00
        material change mirror    FadingSurf 0.00
        material change shininess FadingSurf 0.00
        material change opacity   FadingSurf 0.48
        material change outline   FadingSurf 1.61
        material change outlinewidth FadingSurf 0.19
        material change transmode FadingSurf 0.00	
	}
}
...
...
```
When creating your own material, you can go to `VMD Main` --> `Graphics` --> `Materials`, in the pop out window, you can create a new material and slide the bars to change all kinds of properties. You can use a representation in the graphics window to test the display effect of your new material. Once you are done, you can write those properties into the above code.

# 3. Define functions for quick rendering representations 
Functions can allow us to quickly generate representations to specific systems. We can define such functions in the vmdrc file or in a standalone tcl script. Below is an example when I was studying ion channels using explicit solvent all-atom simulations. If it is defined in the vmdrc, then the function will be defined on start. If it is included in a tcl script, say "channel.tcl", you need to `source channel.tcl` in the VMD shell window, then call the function.

```tcl
proc channel {} {
    #### delete initial line representation for everything
    mol delrep 0 top
    #### make VDW representation for ions
    mol selection {ions}
    mol color Name
    mol representation VDW
    mol material AOChalky
    mol addrep top

    mol selection {ions and ((x-70)**2+(y-70)**2) < 200}
    mol color Name
    mol representation VDW
    mol material AOChalky
    mol addrep top
    
    #### make Cartoon representation for protein
    mol selection {protein}
    mol color Chain
    mol representation NewCartoon
    mol material AOChalky
    mol addrep top

    ### add lipid
    mol selection {lipids and name P}
    mol color ColorID 2
    mol representation VDW
    mol material AOChalky
    mol addrep top

    mol selection {lipids and not name P and not hydrogen}
    mol color ColorID 2
    mol representation Lines
    mol material AOChalky
    mol addrep top

    #### make line representation for water
    mol selection {water}
    mol color Name
    mol representation Lines
    mol material AOChalky
    mol addrep top

    set pbc_dims [pbc get -all]
    set pbc_size [lindex $pbc_dims 0]
    puts "PBC Box Size: $pbc_size"
    mol selection {same residue as (water and ((x-70)**2+(y-70)**2) < 200)}

    mol color Name
    mol representation Lines
    mol material AOChalky
    mol addrep top

    #### turn off axes
    axes location off
    ### set background white
    color Display Background 8

    ### pbc box
    pbc box -width 2.0
}
```


# 4. Use command to control view in the graphics window
I recommend not to use mouse to control the view in the graphics window. Instead, use command to do rotation, translation etc. is controllable and replicable. We can always us `ctrl`+`+` to return to the original view.
```
# rotation
rotate x by 90
rotate y by 90
rotate z by 90

# scale the current view by a factor
scale by 1.5 
```


# 5. Several often used selection syntax 
We can use wildcard to match atom names, residue types  etc.
```
name "O.*"
name "O.?"
```
I also found the following syntax super useful:
```
# the use of "same residue as" and "within"
same residue as ( water and within 10 of protein)

# hide the backbone atoms
(not backbone or name CA) 
(sidechain or name CA)
```


# 6. Self-defined drawings
VMD provides all kinds of drawing methods using `draw` or `graphics` command to create shapes.

## 6.1 Draw arrows
Define a function to draw arrows in VMD:
```tcl
proc vmd_draw_arrow {mol start end {cradius 0.15}} {

    # an arrow is made of a cylinder and a cone
    set middle [vecadd $start [vecscale 0.9 [vecsub $end $start]]]
    graphics $mol cylinder $start $middle radius $cradius
    set cone_radius [expr {3 * $cradius}]
    graphics $mol cone $middle $end radius $cone_radius
}
```


## 6.2 Draw updating COM with spheres
Below is an example where I want to plot the COM of the headgroup of a PIP2 molecule with spheres. The sphere can update its location as we play the frames in the `VMD Main` menu.

```tcl
# Global variable to store the ID of the sphere
set sphere_id ""

# Set up a callback procedure to update the sphere position during animation
proc update_sphere_position {} {
    global sphere_id

    # global vmd_frame
    set selection [atomselect top "chain J and resname POPI and name C12 O2 C13 O3 C14 O4 P4 OP42 OP43 OP44 C15 O5 P5 OP52 OP53 OP54 C16 O6 C11 P O13 O14 O12 O11"]
    set com [measure center $selection weight mass]
    
    # Delete the old sphere
    if {$sphere_id ne ""} {
        draw delete $sphere_id
    }

    draw color green
    set sphere_id [draw sphere $com radius 1.0 resolution 50]
}

# this function is required: https://www.ks.uiuc.edu/Research/vmd/vmd-1.7.1/ug/node140.html
proc sphere_trace {args} {
    update_sphere_position
}

trace variable vmd_frame w sphere_trace
```

## 6.3 Display simulation time updated with frames
Below is an example where I want to display the current simulation time in the graphics window of VMD. the `timeperframe` value may need to be modified according to the trajectory saving frequency.
```tcl
proc enabletrace {} {
  global vmd_frame;
  trace variable vmd_frame([molinfo top]) w drawcounter
}

proc disabletrace {} {
  global vmd_frame;
  trace vdelete vmd_frame([molinfo top]) w drawcounter
}

proc drawcounter { name element op } {
  global vmd_frame;

  draw delete all
  # puts "callback!"
  draw color red
  set timeperframe 0.05
  set time [format "%8.2f ns" [expr $vmd_frame([molinfo top]) * $timeperframe]]
  draw text {50 65 130} "$time" size 2 thickness 4
}

enabletrace
```

## 6.4 Draw amino acid labels
check the tcl script in my github repository:
```text
https://github.com/huangjianhuster/toolbox/blob/main/vmd/draw_label.tcl
```

# 7. Cache secondary structure calculation
When visualizing intrinsically disorder protein trajectories, we usually want the secondary structure to be calculated and rendered for each frame. However, VMD by default only calculates the secondary structure from the first frame.

We can cache secondary structure calculation from a saved vmd session file. We only need to copy the following code block to the end of the vmd session file. And re-open the vmd session file using `vmd -e xxx.vmd`.

```tcl
# start the cache for a given molecule
proc start_sscache {{molid top}} {
    if {! [string compare $molid top]} {
      set molid [molinfo top]
    }
    global vmd_frame
    # set a trace to detect when an animation frame changes
    trace variable vmd_frame($molid) w sscache
    return
}

# remove the trace (need one stop for every start)
proc stop_sscache {{molid top}} {
    if {! [string compare $molid top]} {
      set molid [molinfo top]
    }
    global vmd_frame
    trace vdelete vmd_frame($molid) w sscache
    return
}

# when the frame changes, trace calls this function
proc sscache {name index op} {
    # name == vmd_frame
    # index == molecule id of the newly changed frame
    # op == w

    global sscache_data

    # get the protein CA atoms
    set sel [atomselect $index "protein name CA"]

    ## get the new frame number
    # Tcl doesn't yet have it, but VMD does ...
    set frame [molinfo $index get frame]

    # see if the ss data exists in the cache
    if [info exists sscache_data($index,$frame)] {
        $sel set structure $sscache_data($index,$frame)
        return
    }

    # doesn't exist, so (re)calculate it
    vmd_calculate_structure $index
    # save the data for next time
    set sscache_data($index,$frame) [$sel get structure]

    return
}

start_sscache
```

