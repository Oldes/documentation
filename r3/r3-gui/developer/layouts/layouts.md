R3 GUI Layouts

	Author: Carl Sassenrath, Saphirion AG
	Date: 12-Jan-2013/20:53:11+1:00

=toc

===Concepts

A ~layout~ is a collection of ~faces~.

Layouts are collections of related ~faces~ used for specific parts of a user interface. The faces within a layout are created during the stage of processing where the GUI language (a dialect) is interpreted and its styles create actual faces.

The GUI system has been designed to make layouts very easy to create, debug, and maintain. One of the main goals was for simple GUI definitions to be able to create a wide range of simple, predictable results, but also to allow more sophisticated and elaborate results to be produced using the same set of basic rules.

Basically, layouts provide a way to:

*Group a number of faces together,
*Arrange faces into a desired layout,
*Display a 2D layer, with background or other effects,
*Update and resize those faces when events occur.

Every ~window~ of the GUI display is a layout, and each contains a number of sub-layouts to further divide its area into sections. And, a layout is itself a face, and a GUI is created from one or more layers of layouts, each of which holds faces of its own.

The faces that compose a layout are defined using the GUI language, a special dialect of REBOL. This approach saves time and effort compared to function-based or tag-based languages.

===Using Layouts

Layouts are fundamental to the GUI. When you open a new window like the one defined below, you are creating a layout.

=image graphics/gui-layouts-0a.png

    view [
        text "Example window."
        button "Close" close
    ]

Here the f:view function internally calls the f:make-layout function that takes the GUI dialect block and builds a layout object as a result. The layout contains two ~faces~: one of the text style and one of the button style.

Layouts are often used to divide other layouts with ~sublayouts~:

=image graphics/gui-layouts-0b.png

	view [
	    vpanel [
	        text "Show website"
	        button "Show" browse http://www.rebol.com
	    ]
	    vpanel [
	        text "Example window."
	        button "Close" close
	    ]
	]

This window layout contains two sublayouts.

---Layout Functions

The GUI functions that deal with layouts are:

:view - Create a layout and display it in a window.
:set-layout - Set layout input face values from a block of values.
:get-layout - Get layout input face values as a block.
:clear-layout - Clear layout input face values.
:parse-layout - Parses the layout dialect and returns a block of faces/commands.
:effect-layout - Display a layout transition effect.
:switch-layout - Switch the contents of a layout, using the effect if specified.
:get-parent-layout - Get layout input faces for the contextual parent layout.
:get-layout-var - Get the value of a top level layout/names local variable.

---Layout Object is a Face

Layouts just use the "face" object, with a few extra fields added:

\table
intern   	| object!	| Layout data, mainly for sizing, updated after every content change.||
auto-size	| logic!	| Compute the layout init-size, min-size and max-size after every content change.||
resize	| logic!	| Use the layout init-size as the min-size and max-size of the layout to suppress layout size changes (works only with auto-size).
/table

Additional fields may be appended by special layout styles.

===Layout faces

~Layouts~ provide an efficient and elegant mechanism for laying out the elements of a GUI.

A layout is defined as a collection of faces. When specified within the GUI dialect, the order of the style names determines their positions within the layout area.

For example, using the code below, you'll see the buttons positioned in that same order vertically:

=image graphics/gui-layouts-0.png

	view [
		vpanel [
		    button "First"
		    button "Second"
		    button "Third"
		]
	]

---Layout Variations

The default layout direction depends on the layout style. For example, in a vpanel faces are positioned from top to bottom. However, several variations in layout are possible, and a few predefined styles are available that can be used in various combinations to create a wide variety of layouts.

:vpanel - Its contents are arranged vertically by default, all columns except for the last one contain the same number of faces.
:hpanel - Its contents are arranged horizontally by default, all rows except for the last one contain the same number of faces.
:vgroup - Its contents are arranged vertically by default. Using the RETURN keyword, a user can set every column to have its own length.
:hgroup - Its contents are arranged horizontally by default. Using the RETURN keyword, a user can set every row to have its own length.

The examples below show the main differences between the w:panel and w:group styles.

Here's a w:vpanel layout. Its default orientation is vertical:

=image graphics/gui-layouts-1.png

    view [
        vpanel [
            button "First"
            button "Second"
        ]
    ]

Here's a w:hpanel layout. Its default orientation is horizontal:

=image graphics/gui-layouts-2.png

    view [
        hpanel [
            button "First"
            button "Second"
        ]
    ]

This table summarizes the main differences between these layout styles:

\table

Style |

Direction |

Row/column length ||

vpanel |

vertical |

same for all columns ||

hpanel |

horizontal |

same for all rows ||

vgroup |

vertical |

every column can have individual length ||

hgroup |

horizontal |

every row can have individual length

/table

---Multiple Columns

You can specify the number of columns for a w:hpanel or w:hgroup styles. This is convenient for many layouts, such as input forms that contain multiple labels and input fields. If you don't specify the number, the layout is expected to have just one row.

Here's an example two column hpanel:

=image graphics/gui-layouts-2-col.png

    view [
        hpanel 2 [
            button "First"
            button "Second"
            button "Third"
            button "Fourth"
            button "Fifth"
            button "Sixth"
        ]
    ]

The buttons are laid out in rows first, then columns. The number of rows is determined by dividing the total number of faces by the number of columns.

For w:hgroup layouts you can use the RETURN keyword to specify the number of elements in each row:

=image graphics/gui-layouts-2g-col.png

	view [
	    hgroup [
	        button "First"
	        button "Second"
	        return
	        button "Third"
	        button "Fourth"
	        return
	        button "Fifth"
	        button "Sixth"
	    ]
	]

Note, though, that for the w:hgroup layouts there is no guarantee, that the columns will be maintained, and, above, it is just caused by using the buttons with equal dimensions.

Continuing on, here is a three column w:hpanel layout:

=image graphics/gui-layouts-3-col.png

    view [
        hpanel 3 [
            button "First"
            button "Second"
            button "Third"
            button "Fourth"
            button "Fifth"
            button "Sixth"
        ]
    ]

And if we show four columns, the last row will contain only two buttons.

=image graphics/gui-layouts-4-col.png

    view [
        hpanel 4 [
            button "First"
            button "Second"
            button "Third"
            button "Fourth"
            button "Fifth"
            button "Sixth"
        ]
    ]

---Vertical layouts

If you use a vertical layout, you specify the number of rows instead. When the number of rows is omitted, or defined as zero, the number of rows is equal to the number of faces.

=image graphics/gui-layouts-vert.png

    view [
        vpanel [
            button "First"
            button "Second"
            button "Third"
            button "Fourth"
            button "Fifth"
            button "Sixth"
        ]
    ]

Similarly as above, for vgroup you can use the RETURN keyword to influence the number of elements in every column individually, causing individual columns to have different lengths eventually.

---Combining Layouts

To create a wide variety of layouts, you can combine layout styles in various ways.

For example, here's a hgroup of layouts of buttons:

=image graphics/gui-layouts-combo-1.png

    view [
        hgroup [
            vpanel [
                button "First"
                button "Second"
            ]
            vpanel [
                button "Third"
                button "Fourth"
            ]
            vpanel [
                button "Fifth"
                button "Sixth"
            ]
        ]
    ]

But, here they are stacked in a different order:

=image graphics/gui-layouts-combo-2.png

    view [
        vgroup [
            hpanel [
                button "First"
                button "Second"
            ]
            hpanel [
                button "Third"
                button "Fourth"
            ]
            hpanel [
                button "Fifth"
                button "Sixth"
            ]
        ]
    ]

The example below shows how different numbers of elements can be grouped on separate rows without using panel combo:

=image graphics/gui-layouts-group-1.png

	view [
	    hgroup [
	        button "First"
	        button "Second"
			return
			button "Third"
	        button "Fourth"
	        button "Fifth"
	        return
	        button "Sixth"
	        button "Seventh"
	        button "Eighth"
	        button "Nineth"
	    ] options [pane-align: 'center]
	]

, and this is the same layout being resized to be wider:

=image graphics/gui-layouts-group-1a.png

Notice also, how the PANE-ALIGN attribute influenced the look of the layout.

---Input Form Layout

Of course, the above layouts are just for illustration. A real layout might look more like:

=image graphics/gui-opinion-form.png

Opinion survey form (alpha skin)

And, its code is:

	view [
	    title "Opinion Survey"
	    text "Do you want programs to be easy to build?"
	    hpanel 2 [
	        label "Answer:"
	        hgroup [
	            radio "Agree"
	            radio "Disagree"
	            radio "Not sure"
	        ]
	        pad
	        check "I'm a programmer."
	        pad
	        check "I am also a REBOL expert."
	        label "Name:"
	        field
	        label "Comment:"
	        ;area
	    ]
	    hgroup [
	        button "Submit" submit http://www.rebol.net/cgi/submit.r
	        button "Reset"  reset
	        button "Cancel" close
	    ]
	]

For more examples, see ~guide~.

===Layout Spacing

In addition to the box model used, which defines individual margins, borders, etc. for every face, all layout styles define the spacing attribute for their layout, which specifies the additional space inserted between subsequent columns and rows.

Here are a few layouts with specified options:

=image graphics/gui-layouts-combo-3.png

	view [
	    vpanel [
	        button "1"
	        button "2"
	        button "3"
	    ]
	    vpanel [
	        button "1"
	        button "2"
	        button "3"
	    ] options [margin: [30x30 30x30]]
	    vpanel [
	        button "1"
	        button "2"
	        button "3"
	    ] options [
	        padding: [30x30 30x30]
	    ]
	    vpanel [
	        button "1"
	        button "2"
	        button "3"
	    ] options [
	        spacing: 20x20
	    ]
	]

---Extra Pad Spacing

From time to time you may need additional spacing between faces in a layout. The w:pad style has been defined to help with this.

The w:pad style can be used with or without optional size information. When used alone, it expands space automatically. Or, you can give it an exact size.

Here are a few examples:

=image graphics/gui-layouts-combo-4.png

	view [
	    vpanel [
	        button "1"
	        button "2"
	        button "3"
	    ]
	    vpanel [
	        button "1"
	        pad
	        button "2"
	        button "3"
	    ]
	    vpanel [
	        button "1"
	        pad 10x20
	        button "2"
	        button "3"
	    ]
	]

Note that a pad is an actual face, so it will count as the element of a cell.

=image graphics/gui-layouts-combo-5.png

    view [
        hpanel 2 [
            button "1"
            button "2"
            pad
            button "3"
        ]
    ]

===Layout Sizing

When creating a layout, it is possible to either define its initial size, or to let the layout auto-size for you.

When auto-sizing, the initial size of the layout is determined by its elements and by the size available for it (for example, by the size of the screen or window). And, within a layout, the rows and columns that make up its layout may be of different widths and heights.

In general the rules are:

*The widest element of a column will set the column width.
*The tallest element of a row will set the row height.
*Columns and rows can be smaller or larger than these values, depending on the availability of space for expansion.

---Init-size, Min-size, Max-size

More specifically, the sizes of elements are determined by their sizes, as set by their styles or by their face instance. Each style, hence each face, can specify three sizes:

:init-size - can be called the natural size of the face, this is the size it "wants to be"
:min-size - the smallest size allowed
:max-size - the largest size allowed

These sizes are all pair! values, so they include both the x (width) and y (height) values.

For example, a text w:field can normally be 300x24, but be allowed to shrink to 100x24 or expand to 500x24. Such a style definition would look like this:

    my-field: field [
        facets [
            init-size: 300x24
            min-size: 100x24
            max-size: 500x24
        ]
    ]

---Size Computation

The size of a layout is determined by the combined sizes of its elements (faces within it) or by a size specified for it (from its options).

Before any hpanel/vpanel sizing can be done, the system must determine for each column and each row:

#the init-size (the largest init-size of the row/column elements)
#the min-size (the largest min-size of the row/column elements)
#the max-size (the largest max-size of the row/column elements)

Hgroup/vgroup differs in that e.g. for a hgroup the init-size, min-size and max-size of every row are computed, columns aren't maintained in hgroups. (In vgroups, rows aren't maintained.)

Now the system can update the layout, i.e. compute (for vpanel/hpanel):

#the pane init-size (as the sum of the line and column init-sizes)
#the pane min-size (analogically)
#the pane max-size (analogically)
#the line minification index (lines are ordered by their ability to shrink)
#the line magnification index (lines are ordered by their ability to expand)
#the column minification index
#the column magnification index
#the layout init-size - if the layout has the auto-size? property (e.g. if the layout init-size is unknown, or set to 0x0, or if the auto-size? property is specified as an option), use the pane init-size computed above and add the layout box-model attributes. If the layout does not auto-size, use the init-size specified.
#the layout min-size - if the layout has the resizes? property, use the above pane min-size, and add the layout box-model attributes, otherwise use the init-size to effectively disable resizing
#the layout max-size

For an updated layout, the actual resizing can be computed as follows:

Vertical and horizontal resizing are performed independently on each other, thus, it is possible to expand a layout in the horizontal direction, and shrink it in the vertical direction at the same time.

When resizing a hpanel/vpanel, the goal is to resize its rows and columns so, that all the available space is used if possible, and that the individual rows and columns are resized at the same (or similar) rate if possible.

To facilitate that, the flexibilities (maximal expansion/shrinking rates) of all rows/columns are computed, and the rows/columns with lowest flexibilities are resized first. Then, the rest of the space available can be taken by the rest of the rows (columns) with greater flexibilities.

Layouts can be resized by the user, such as when a window size is changed, or by the program itself.

When a layout is resized, the layout is recomputed according to the same rules as above. All of the faces within it will be resized and repositioned as necessary, including all sub-layouts.

---Examples

This code provides a good comparison for how GUI faces are resized according to their max-size option. The window shows five groups, each that contains three colored boxes. Each box is the same style, but changes its max-size option to a different value.

Resize the window to see the resizing of each box:

	stylize [
	    boxa: box [
	        facets: [
				init-size: 32x32
				bg-color: none
			]
			options: [
				init-size: [pair!]
				box-color: [tuple!]
			]
	        draw: [
				pen 0.0.0
				line-width 1.2
				fill-pen box-color
				box 1x1 (gob-size - 2) 3
	        ]
			actors: [
				on-draw: [
					arg
				]
			]
	    ]
	    pada: pad [
	        facets: [max-size: 10x10]
	    ]
	]

	view [
	    hgroup [
	        boxa red
	        boxa green
	        boxa blue
	    ]
	    hgroup [
	        boxa red   options [max-size: 100x32]
	        boxa green options [max-size: 200x32]
	        boxa blue  options [max-size: 400x32]
	    ]
	    hgroup [
	        boxa red   options [max-size: 100x32]
	        boxa green options [max-size: 2000x32]
	        boxa blue  options [max-size: 4000x32]
	    ]
	    hgroup [
	        boxa red   options [max-size: 100x32]
	        pad
	        boxa green options [max-size: 2000x32]
	        pad
	        boxa blue  options [max-size: 4000x32]
	    ]
	    hgroup [
	        boxa red   options [max-size: 100x32]
	        pada
	        boxa green options [max-size: 2000x32]
	        pada
	        boxa blue  options [max-size: 4000x32]
	    ]
	]

Here's how the window is first opened:

=image graphics/gui-layouts-sizing-1.png

And if it's resized to a wider shape, it will look like:

=image graphics/gui-layouts-sizing-2.png

Here are some features to observe:

*The first group of boxes all expand in equal proportion.
*The second group expands in equal proportion, until the max-size of the green box is reached. Afterwards, the green box stops expanding leaving the space for the other boxes. Also note that if you expand the window large enough, each of the boxes will stop expanding at their max-size values. (You will not see that effect if the window is too small.)
*The third group shows a more extreme example, where the green and blue boxes are expanded by a lot more than the red box. The red box has only expanded by a small amount (if at all).
*The fourth group adds some w:pad faces between the boxes. Notice that by default, the pads add substantial space.
*The fifth group uses the w:pada style which limits the max-size. Notice that the pad spaces are much smaller.

An analogical example can be written using just one hgroup and the RETURN keyword:

	stylize [
	    boxa: box [
	        facets: [
				init-size: 32x32
				bg-color: none
			]
			options: [
				init-size: [pair!]
				box-color: [tuple!]
			]
	        draw: [
				pen 0.0.0
				line-width 1.2
				fill-pen box-color
				box 1x1 (gob-size - 2) 3
	        ]
			actors: [
				on-draw: [
					arg
				]
			]
	    ]
	    pada: pad [
	        facets: [max-size: 10x10]
	    ]
	]

	view [
	    hgroup [
	        boxa red
	        boxa green
	        boxa blue
	        return
	        boxa red   options [max-size: 100x32]
	        boxa green options [max-size: 200x32]
	        boxa blue  options [max-size: 400x32]
	    	return
	        boxa red   options [max-size: 100x32]
	        boxa green options [max-size: 2000x32]
	        boxa blue  options [max-size: 4000x32]
	    	return
	        boxa red   options [max-size: 100x32]
	        pad
	        boxa green options [max-size: 2000x32]
	        pad
	        boxa blue  options [max-size: 4000x32]
	    	return
	        boxa red   options [max-size: 100x32]
	        pada
	        boxa green options [max-size: 2000x32]
	        pada
	        boxa blue  options [max-size: 4000x32]
	    ]
	]

=image graphics/gui-layouts-sizing-3.png

===Visibility of faces

The SHOW-FACE function is defined to set the visibility/resizeability of a face in a layout to one of the following states:

#VISIBLE - the face is visible and resizes/repositions with the layout
#HIDDEN - the face is invisible, but it resizes with the layout, occupying the same space as if it were visible
#IGNORED - the face is invisible, and it does not take any space
#FIXED - the face is visible, but it does not resize/reposition with the layout

=image graphics/gui-layouts-visibility.png

Example:

update-btn: func [
        btn [object!]
        id [integer!]
] [
        id: pick [visible hidden ignored fixed] id
        if id = 'fixed [
                btn/gob/offset: random 100x100
        ]
        show-face btn id
]

view [
        title "SHOW? modes test example"
        hpanel 3 [
                b1: button "one"
                b2: button "two"
                b3: button "three"
                b4: button "four"
                b5: button "five"
                b6: button "six"
        ]
        hgroup [
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b1 arg]]
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b2 arg]]
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b3 arg]]
                return
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b4 arg]]
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b5 arg]]
                drop-down ["visible" "hidden" "ignored" "fixed"]
                	on-action [do [update-btn b6 arg]]
        ]
]

===Linking Related Faces

Within a GUI there are times when you need to link or attach one face to another. Change to one of the faces affects the other. For example, a scroll bar may cause a text layout to scroll its contents.

There are two methods of relating faces: ~named relations~ and ~proximity relations~. They are explained below.

---Named Relations

You can give a name to a face and that name can be used from other faces to perform specific actions.

For example:

    prog: progress
    slider attach 'prog

Here w:attach is a ~reactor~ that "connects" the slider value to the progress bar. The w:prog face name is used to do that, and be sure to note that the name must be a
literal word where it is referenced.

Warning: It should be noted that all names are scoped to their primary layout, so you can refer to a name from other layouts within the primary layout.

For example, this still works, even though the w:prog name is used from a different layout:

    prog: progress
    hpanel [
        text "Move this slider:"
        slider attach 'prog
    ]

Note: the w:names facet of a parent controls the scope of names.

---Proximity Relations

Some faces can automatically attach themselves to other faces that are nearby.

For example, a w:scroller will attach itself automatically to any prior face that includes an w:on-scroll actor. This feature makes it easy to attach scroll bars to text areas and layouts without requiring names.

Here is an example:

    hgroup [
        text-area "This is an example"
        scroller
    ]

This works the same way as if you wrote:

    hgroup [
        ta: text-area "This is an example"
        scroller attach 'ta
    ]

===Transition Effects

Animated transition effects can be used to switch from one layout to another. The effects are specified as an argument to the f:switch-layout function.

Currently, these effects are defined:

:fly-right - fly on from left to right
:fly-left - fly on from right to left
:fly-up - fly on from bottom to top
:fly-down - fly on from top to bottom

===Other Predefined Layouts

===Defining Layout Styles
