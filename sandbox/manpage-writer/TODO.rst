TODOs 
=====

:Date: $Date$

special chars
-------------

from roff(7)::

  A few characters commonly seen on keyboards are treated specially by the roff
  language and may not look correct in output if  used  unthinkingly;  they are
  the  (double)  quotation mark ("), the neutral apostrophe ('), the minus sign
  (-), the backslash (\), the caret or circumflex accent (^), the grave accent
  (`), and  the  tilde (~).  All of these and more can be produced with special
  character escape sequences; see groff_char(7).


Issues
------

* lines starting with a ``{not typeset}  abc``

* #482 no default formatting ... use macros or leave to writer

* How to typeset command/manpage names in text.

  There are conflicting traditions and conventions on these points.
  groff_man_style(7) has recommendations.

              Use bold for literal portions of syntax synopses, for
              command‐line options in running text, and for literals
              that are major topics of the subject under discussion; for
              example, this page uses bold for macro, string, and
              register names.  In an .EX/.EE example of interactive I/O
              (such as a shell session), set only user input in bold.

              Use italics for file and path names, for environment
              variables, for C data types, for enumeration or
              preprocessor constants in C, for variant (user‐
              replaceable) portions of syntax synopses, for the first
              occurrence (only) of a technical concept being introduced,
              for names of journals and of literary works longer than an
              article, and anywhere a parameter requiring replacement by
              the user is encountered.  An exception involves variant
              text in a context already typeset in italics, such as file
              or path names with replaceable components; in such cases,
              follow the convention of mathematical typography: set the
              file or path name in italics as usual but use roman for
              the variant part (see .IR and .RI below), and italics
              again in running roman text when referring to the variant
              material.

  Plan 9 from User Space troff and groff 1.23.0 support an ``MR`` macro
  for the specific purpose of setting man page cross references.  It is
  reasonable to assume that groff 1.23.0 has not propagated yet to every
  platform Python docutils would like to support yet.  You can wait
  until it has, or supply a fallback definition in the man(7) documents
  you generate. ::

   .\" Define fallback for groff 1.23's MR macro if the system lacks it.
   .nr df 0 \" do fallback?
   .if !\n(.f           .nr df 1 \" mandoc
   .if  \n(.g .if !d MR .nr df 1 \" older groff
   .if !\n(.g           .nr df 1 \" non-groff *roff
   .if \n(df \{\
   .de MR
   .ie \\n(.$=1 \
   .I \%\\$1
   .el \
   .IR \%\\$1 (\\$2)\\$3
   ..
   .\}
   .rr df

  see groff_man_style ::

   .SH "See also"
   .
   .MR \%tbl 1 ,
   .MR \%eqn 1 ,
   and
   .MR \%refer 1
   are preprocessors used with man pages.
   .
   .MR man 1
   describes the man page librarian on your system.

  but the rST source would be more like this ::

   See also
   ========

   tbl(1), eqn(1), and refer(1) are preprocessors used with man pages.
   man(1) describes the man page ...

  so the MR things need to be found with regexp or marked with a role/directive ?
  or a reference type man:tbl(1) 

  Note:  The ``\%`` is :

    a  word  may be broken at hyphens, at ``\%`` or ``\:`` escape sequences

    whatfore at the word start ?

    Branden Robinson writes:

     You will not need to generate the arguments to the MR macro with a leading
     \% escape sequence. ... see feature request #107

     \% Control hyphenation.  The location of this escape
        sequence within a word marks a hyphenation point,
        supplementing groff’s automatic hyphenation patterns.  At
        the beginning of a word, it suppresses any hyphenation
        breaks within except those specified with \%.* groff_man_style on 

   Hyperlink macros
       Man  page  cross references like ls(1) are best presented with .MR.  Text may be
       hyperlinked to email addresses with .MT/.ME or other URIs with .UR/.UE.   Hyper‐
       linked  text  is  supported  on  HTML and terminal output devices; terminals and
       pager programs must support ECMA-48 OSC  8  escape  sequences  (see  grotty(1)).
       When  device support is unavailable or disabled with the U register (see section
       “Options” below), .MT and .UR URIs are rendered between angle brackets after the
       linked text.

       .MT, .ME, .UR, and .UE are GNU extensions not defined on systems  running  AT&T,
       Plan 9, or Solaris troff; see an-ext.tmac in section “Files” below.  Plan 9 from
       User Space's troff implements .MR.

       The arguments to .MR, .MT, and .UR should be prepared for typesetting since they
       can appear in the output.  Use special character escape sequences to encode Uni‐
       code  basic  Latin  characters  where  necessary, particularly the hyphen-minus.
       (See section “Portability” below.)  URIs can be lengthy; rendering them can  re‐
       sult  in jarring adjustment or variations in line length, or troff warnings when
       a hyperlink is longer than an output  line.   The  application  of  non-printing
       break point escape sequences \: after each slash (or series thereof), and before
       each  dot  (or  series  thereof)  is recommended as a rule of thumb.  The former
       practice avoids forcing a trailing slash in a URI onto a separate  output  line,
       and  the  latter  helps the reader to avoid mistakenly interpreting a dot at the
       end of a line as a period (or multiple dots as an ellipsis).  Thus, ::

              .UR http://\:example\:.com/\:fb8afcfbaebc74e\:.cc

       has several potential break points in the  URI  shown.   Consider  adding  break
       points  before  or after at signs in email addresses, and question marks, amper‐
       sands, and number signs in HTTP(S) URIs.  The formatter removes  \:  escape  se‐
       quences  from  hyperlinks  when supplying device control commands to output dri‐
       vers.

* groff.1 at end of manpage has ::

    .TP
    \[lq]groff guess\[rq] utility:
    .MR grog 1 \" #43
    .
    .
    .\" Restore compatibility mode (for, e.g., Solaris 10/11).
    .cp \n[*groff_groff_1_man_C]
    .do rr *groff_groff_1_man_C
    .

  means what ? should we do this too ?


Used macros ... to be completed
-------------------------------

       .TP [indentation]
              Set  a  paragraph  with a leading tag, and the remainder of the paragraph
              indented.  A one-line input trap is planted; text on the next line, which
              can be formatted with a macro, becomes the tag, which is  placed  at  the
              current  left  margin.   The  tag  can be extended with the \c escape se‐
              quence.  Subsequent text is indented by indentation, if specified, and by
              the amount of the IN register otherwise.  If the tag is not  as  wide  as
              the indentation, the paragraph starts on the same line as the tag, at the
              applicable indentation, and continues on the following lines.  Otherwise,
              the  descriptive  part  of the paragraph begins on the line following the
              tag.

       .TQ    Set an additional tag for a paragraph tagged with .TP.  An input trap  is
              planted as with .TP.

              This  macro  is  a  GNU  extension  not  defined on systems running AT&T,
              Plan 9, or Solaris troff; see an-ext.tmac in section “Files” below.



Notes
-----

* Images and equations are discouraged.

* Lists in admonitions are not intended.

* Branden Robinson on hyphens, minus <https://lwn.net/Articles/948563/> :

    Unicode has a hyphen (U+2010) and a minus sign (U+2212), and "obviously", a
    person should input those code points for their distinct purposes.

    This works great until someone needs needs to input a "literal" for an
    overloaded code point in the Basic Latin code chart that has syntactical
    significance to something like a shell prompt or a language compiler. Then
    they need that hen's tooth U+002D code point, even though it is meaningful
    _only_ for talking to computers, and not for any other domain of discourse.
    And that's not even taking into account the folks who ride in an want
    distinguishable en dashes, em dashes, figure dashes, and others the LWN
    article didn't mention. Fitting distinguishable glyphs for these into a
    half-width character cell even with a fair number of pixels in the
    horizontal dimension (say, more than 8) starts to become a real pickle.

  and in groff_man_style ::

    \-   Minus sign or basic Latin hyphen-minus. This escape sequence produces
         the Unix command-line option dash in the output.  
         “-” is a hyphen in the roff language; some output devices replace it
         with U+2010 (hyphen) or similar.

Discussions
-----------

* Encoding declaration ``'\" t -*- coding: ISO-8859-1 -*-``
  in first line.

  The part after ``t`` is a GNU Emacs convention.  A *groff* program
  called *preconv*\(1) understands it, but GNU *troff*\(1) itself does
  not.

  The ``t`` is part of a *man*\(1) convention; GNU *troff*\(1) doesn't
  understand it, either (but since *tbl*\(1) is a *roff* preprocessor,
  it's too late by the time ``troff`` sees the input anyway).

* BUT if UTF-8 is declared tables are no longer processed.

* BUT we have a comment there and the macros following it

* Input and output encoding are problematic at least.

* input/optionstoo.txt:23: (ERROR/3) Unexpected indentation.
  is what it is ... leave it so.

* doublespace after end of sentence in manpages ? 

  see https://sourceforge.net/p/docutils/bugs/427/

  Chicago manual of style ... hard to automate.

* Check ``docs/user/manpage.txt``

* escape double quotes in macro arguments ?

  Use the special character escape sequence ``\(dq``.
  groff_man_style(7) explains.

       \(dq   Basic Latin quotation mark (double quote).  Use in macro
              calls to prevent ‘"” from being interpreted as beginning a
              quoted argument, or simply for readability.

                     .TP
                     .BI "split \(dq" text \(dq

* How to write long syntax lines.

  groff_man_style(7) explains.::

       \newline
              Join the next input line to the current one.  Except for
              the update of the input line counter (used for diagnostic
              messages and related purposes), a series of lines ending
              in backslash‐newline appears to groff as a single input
              line.  Use this escape sequence to split excessively long
              input lines for document maintenance.

* Line ends around email or web addresses in texts.

  The ``UE`` and ``ME`` macros accept an argument, which is appended to
  the link text without intervening space.

  groff_man_style(7) explains.

       \c     End a text line without inserting space or attempting a
              break.  Normally, if filling is enabled, the end of a text
              line is treated like a space; an output line may be broken
              there (if not, an adjustable space is inserted); if
              filling is disabled, the line will be broken there, as in
              .EX/.EE examples.  The next line is interpreted as usual
              and can include a macro call (contrast with \newline).  \c
              is useful when three font styles are needed in a single
              word, as in a command synopsis.

                     .RB [ \-\-stylesheet=\c
                     .IR name ]

              It also helps when changing font styles in .EX/.EE
              examples, since they are not filled.

                     .EX
                     $ \c
                     .B groff \-T utf8 \-Z \c
                     .I file \c
                     .B | grotty \-i
                     .EE

  Here's an example using groff's ``MT`` and ``ME`` macros.::

   Mail the maintainer (\c
   .MT maint@example.com
   Arthur Pewtey
   .ME )
   to submit patches.

  How to distinguish something is inline or not in the writer 
  so to maybe put long urls after the current paragraph ?
    

