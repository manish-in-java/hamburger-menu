# Pure CSS multi-level hamburger menu

This is a [hamburger menu](https://uxplanet.org/the-ultimate-guide-to-the-hamburger-menu-and-its-alternatives-e2da8dc7f1db)
solution developed using only HTML 5 and CSS 3. It has been tested on a
wide variety of browsers, including mobile browsers, and found to be
working satisfactorily well.

## The need

An application I worked on had a nested menu that could have any number
of levels, without any restriction on the depth. This menu worked
really well on large screens. However, due to the large size of the
menu, it could not be rendered well on mobile devices due to the limited
height and width available on those devices. Therefore, I initially hid
the menu using a CSS media query for screen resolutions below a certain
minimum width, making the menu unavailable on mobile devices.

I wanted to extend this solution to work on mobile devices as well,
given a significant amount of traffic to our application from mobile
devices. After a lot of deliberation and reading I settled on
developing a hamburger menu for mobile devices, preferably with the
same HTML as that used for the desktop menu so that I did not have to
maintain two different HTML - one for the desktop menu, and another
for the hamburger menu.

I started by listing our requirements for the hamburger menu, which
came down to the following:

1. **Must-have**: Work consistently across common mobile browsers -
Chrome on Android, Safari on iOS and Edge on Windows Mobile (yes, at
the time I developed the hamburger menu, I had users on Windows
Mobile);
1. **Must-have**: Provide an intuitive navigation solution for working
with menus any levels deep, without any restrictions on the nesting
levels;
1. **Should-have**: Allow users to navigate both forward (drill-down)
and backward (drill-up);
1. **Should-have**: Use only HTML 5 + CSS 3; no JavaScript, if possible;
1. **Could-have**: Work as a pure CSS 3 solution with the existing HTML
I already had.

## Inspirations

During my search, I came across [NavNav.co](https://navnav.co). This
website is a collection of useful examples of responsive navigation
solutions available on the internet. It is therefore a great resource
to see many possible solutions for responsive navigation in one place.

I came across [this example](https://codepen.io/Shven/pen/chKqD) on
NavNav.co that fit all our needs listed above perfectly. The only
shortcoming I noticed with this example was its lack of ability to
close the whole menu in one go, if the user is at a deeper nesting
level.

This final shortcoming got plugged using guidance from
[this article](https://codersblock.com/blog/checkbox-trickery-with-css/)
(more on this later).

## The solution

#### *HTML*

##### Step 1: Rendering the menu

The menu is rendered using the HTML 5 `nav` and `menu` tags. It
therefore looks like this:

```
<nav>
  <main>
    <menu>
      ...
    </menu>
  </main>
</nav>
```

##### Step 2: Rendering menu items

Each menu item within the menu is rendered using the HTML 5 `menuitem`
tag, as follows:

```
<menuitem>
  ...
</menuitem>
```

##### Step 3: Adding submenus to menu items

Submenus are nested inside a menu item by repeating the HTML for the
menu, as an example:

```
<menuitem>
  <span>...</span>
  <nav>
    <main>
      <menu>
        ...
      </menu>
    </main>
  </nav>
</menuitem>
```

This allows menus to have as many levels as required, since the HTML
for each level is the same.

##### Step 4: Control whether a menu or submenu is displayed

To control whether a menu or submenu should be displayed (or hidden), a
checkbox is added just before the menu. How this controls the menu
visibility is explained below under the **CSS** section.

```
<input id="menu" type="checkbox">
<nav>
  <main>
    <menu>
      ...
    </menu>
  </main>
</nav>
```

We intend to display a menu if its immediately preceding checkbox is
checked (and hide it otherwise).

Finally, the state of each checkbox is controlled using a button, which
will be rendered using CSS. The buttons rendered using the `label` tag
and styled using CSS (explained later).

```
<input id="menu" type="checkbox">
<nav>
  <main>
    <label class="menu-toggle" for="menu"><span>&nbsp;</span></label>
    <menu>
      ...
    </menu>
  </main>
</nav>
```

The value of the `for` attribute of each `label` element must match the
value of the `id` element of the `input` element it is expected to
toggle. In the snippet above, the `input` element has the `id`
`menu`.

##### Step 5: Closing the whole menu in one go

Given that the menu can have an unlimited number of nested levels, we
would like to have the ability to close the whole menu if a user is at
a deep nested level within the menu. Since our menus get hidden
automatically if their associated checkboxes are unchecked, we need a
way to uncheck all the checkboxes in the menu, in one go. Although this
can be achieved using JavaScript, fortunately, there is a pure HTML way
to achieve this.

To achieve what we want, we wrap the whole HTML in an HTML `form`, and
add a `reset` button to the form.

```
<form action="javascript:">
  <input id="menu" type="checkbox">
  <input id="menu-reset" type="reset">
  <nav>
    <main>
      <label class="menu-toggle" for="menu"><span>&nbsp;</span></label>
      <label class="menu-close" for="menu-reset"><span>&nbsp;</span></label>
      <menu>
        ...
      </menu>
    </main>
  </nav>
</form>
```

`action="javascript:"` is added to the form tag to ensure that the form
does not get submitted in any way.

#### *CSS*

##### Step 1: Hide unnecessary elements

The checkboxes and the reset button are simply for controlling whether
and when menus should be displayed. The first step is therefore to
hide them so that they do not appear on the UI.

```
input[type="checkbox"],
input[type="reset"] {
  display: none !important;
}
```

##### Step 2: Hide the hamburger menu by default

We hide the menu off the visible screen area by default. This way, the
menu remains hidden out of sight, until the user requests to see it.

```
nav {
  height: 100vh;
  left: 0;
  position: absolute;
  top: 0;
  transform: translateX(-300px);
  transition: transform .8s ease-in-out;
  width: 300px;
  z-index: 999;
}
```

`width: 300px` gives the menu (and its submenus) a fixed width of
`300px`. `position: absolute` and `left: 0` place the starting position
of the menu at 0 pixels from the left of its container.
`transform: translateX(-300px)` shifts it to the left by `300px`,
effectively hiding it from the view, since the shifted amount is the
same as the menu width.

##### Step 3: Display the hamburger icon

We need to display the hamburger icon &#x2630; that the users can click
to toggle the menu state. Since the checkboxes have been hidden earlier,
we will style the associated labels to achieve the effect we want.

```
1.   nav label {
2.     cursor: pointer;
3.     font-weight: normal;
4.     margin: 0;
5.   }
6.
7.   nav label:focus { outline: none; }
8.
9.   nav label.menu-toggle {
10.    background: #D8F2E5;
11.    display: block;
12.    height: 50px;
13.    line-height: 0;
14.    padding: 0;
15.    position: absolute;
16.    right: -50px;
17.    text-indent: -9999px;
18.    width: 50px;
19.  }
20.
21.  nav label.menu-toggle>span,
22.  nav label.menu-toggle>span:after,
23.  nav label.menu-toggle>span:before {
24.    background: #000;
25.    display: block;
26.    height: 3px;
27.    pointer-events: none;
28.    transition: transform .4s ease-in-out,background-color .4s ease-in-out 0s;
29.    width: 20px;
30.  }
31.
32.  nav label.menu-toggle>span {
33.    display: block;
34.    margin-left: 15px;
35.    margin-top: 50%;
36.    position: relative;
37.  }
38.
39.  nav label.menu-toggle>span:after,
40.  nav label.menu-toggle>span:before {
41.    content: "";
42.    position: absolute;
43.    transform: rotate(0) translateY(0);
44.  }
45.
46.  nav label.menu-toggle { margin-top: -30px; }
47.
48.  nav label.menu-toggle>span:after {
49.    transform: translateY(6px);
50.  }
51.
52.  nav label.menu-toggle>span:before {
53.    transform: translateY(-6px);
54.  }
```

Lines `1-20` simply change the default look and feel for the `label`s
to make them look like icons.

Lines `21-30` turn the `span` tag inside each `label` into a bar of
height `3px` and width `20px`. These lines also add two pseudo elements
before and after the `span` tag (`span:before` and `span:after`), with
the same properties as the `span` itself. Lines `48-50` push the bar
generated by the `span:after` pseudo element `6px` down, while lines
`52-54` push the bar generated by the `span:before` pseudo element
`6px` up, compared to the middle bar rendered by the `span` element
itself.

##### Step 4: Display a menu when its associated checkbox is checked

Since the hamburger icons are simply labels associated with checkboxes,
clicking them toggles the state of the associated checkboxes. We take
advantage of this to also toggle the visibility of the associated menus,
using CSS selectors.

```
input[type="checkbox"]:checked ~ nav { transform: translateX(0); }
```

The CSS selector `:checked` applies only to `input` elements that are
in checked state. The selection `~` selects a sibling element. Combining
these two selectors selects a `nav` element that is a sibling of an
`input` element that is in checked state. `transform: translateX(0)`
changes the `x` co-ordinate of the menu from `-300px` (the default)
to `0`. Since the menu is absolutely positioned with `left: 0`, this
change in the `x` co-ordinate brings the menu into view.

If a label is clicked again, it unchecks its associated checkbox,
resetting the `x` co-ordinate of the associated menu.

##### *Interlude 1: Show and hide menus with a nice animation*

The CSS for the menus had `transition: transform .8s ease-in-out;`. This
line adds a nice animation to the menus, giving them a slide-in and
slide-out look.

##### Step 5: Allow users to go back to the previous level

We have already styled `label` elements so that they appear as hamburger
icons instead of their default look-and-feel. We did this so that users
could click the hamburger icons to view their associated menus. Noting
that each `label` toggles the state of its associated checkbox, we know
that if a user clicks a hamburger icon again, it would simply uncheck
the associated checkbox, thereby hiding the associated menu as well.
We make this more obvious to the user by changing the hamburger icon
to a back button.

```
input[type="checkbox"]:checked ~ nav label.menu-toggle > span:after,
input[type="checkbox"]:checked ~ nav label.menu-toggle > span:before {
  width: 14px;
}
input[type="checkbox"]:checked ~ nav label.menu-toggle > span:after {
  transform: rotate(-45deg) translateY(-4px) translateX(4px);
}
input[type="checkbox"]:checked ~ nav label.menu-toggle > span:before {
  transform: rotate(45deg) translateY(4px) translateX(4px);
}
```

These lines reduce the width of the top and bottom lines of the
hamburger icon from `20px` to `14px` and rotate them to turn the
three lines of the hamburger icon into a back arrow &#8592;.

##### Step 6: Closing the whole menu in one go

We added a `reset` button and an associated `label` to the HTML so that
users could close the whole menu in one go. We now add the associated
CSS.

```
nav label.menu-close {
  background: #D8F2E5;
  display: block;
  height: 50px;
  line-height: 0;
  padding: 0;
  position: absolute;
  right: -50px;
  text-indent: -9999px;
  width: 50px;
}

nav label.menu-close>span:after,
nav label.menu-close>span:before {
  background: #000;
  display: block;
  height: 3px;
  pointer-events: none;
  transition: transform .4s ease-in-out,background-color .4s ease-in-out 0s;
  width: 20px;
}

nav label.menu-close>span,
nav label.menu-toggle>span {
  display: block;
  margin-left: 15px;
  margin-top: 50%;
  position: relative;
}

nav label.menu-close>span:after,
nav label.menu-close>span:before {
  content: "";
  position: absolute;
  transform: rotate(0) translateY(0);
}

nav label.menu-close {
  margin-top: 21px;
  opacity: 0;
  transition: all .4s ease-in;
}

nav label.menu-close>span:after {
  transform: rotate(45deg);
}
nav label.menu-close>span:before {
  transform: rotate(-45deg);
}

input[type="checkbox"]:checked ~ nav label.menu-close {
  opacity: 1;
}
```

The CSS is similar to that for the hamburger icons. The only difference
is that where the hamburger icons has three bars, the close icon has
only two, arranged as a cross (X). The last three lines simply ensure
that the close icon is visible only when the menu is visible.

## Demo

[See this](http://htmlpreview.github.com/?https://github.com/manish-in-java/hamburger-menu/blob/master/menu.html)
in action.

## Acknowledgements

Neither the idea(s), nor the implementation for this solution are my
own. I am grateful to a number people whose ideas I adapted to come up
with the solution posted here.

First and foremost, my sincere thanks to
[Sven Aarts](https://twitter.com/shven), who published this
[example](https://codepen.io/Shven/pen/chKqD), and which I adapted to
create the solution published here.

Next, I would like to thank [Will Boyd](https://twitter.com/lonekorean)
for
[this great article](https://codersblock.com/blog/checkbox-trickery-with-css/)
on playing with HTML checkboxes using CSS. This article gave me the
inspiration to add a "close" button to my solution that closes all
open menus in one go.

Finally, kudos to [Mills Digital LLC](http://www.bymills.com) for
putting up NavNav.co and collecting all the awesome HTML, CSS and
JavaScript solutions featured there.

## License
This application and its associated source code in its entirety is being made
available under the following licensing terms.

    Permission is hereby granted, free of charge, to any person obtaining a copy of
    this software and associated documentation files (the "Software"), to deal in the
    Software without restriction, including without limitation the rights to use, copy,
    modify, merge, publish, distribute, sublicense, and/or sell copies of the Software,
    and to permit persons to whom the Software is furnished to do so, subject to the
    following conditions:

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
    INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
    PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
    HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF
    CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE
    OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
