#+TITLE: Xi Aleste
#+SUBTITLE: Harware Manual
#+AUTHOR: Valerya Pudova
#+EMAIL: valery.hww@arcadia.com
#+DATE: 09.04.2022

#+latex_header: \usepackage[utf8]{inputenc}
#+latex_header: \usepackage[T2A]{fontenc}
#+latex_header: \usepackage[greek, russian, english]{babel}
#+OPTIONS: ^:nil
#+OPTIONS: toc:nil author:nil date:nil
#+OPTIONS: ^:{}
#+STARTUP: hideeverything
#+STARTUP: inlineimages
#+latex_header_extra: \usepackage[usenames]{xcolor}
#+latex_header_extra: \usepackage[framemethod=TikZ]{mdframed}

# Gray box around 'mdframed'
#+LATEX_HEADER_extra: \BeforeBeginEnvironment{minted}{\begin{mdframed}}
#+LATEX_HEADER_extra: \AfterEndEnvironment{minted}{\end{mdframed}}
#+LATEX_HEADER_extra: \mdfsetup{ middlelinecolor=gray, middlelinewidth=2pt, backgroundcolor=gray!20, roundcorner=4pt, nobreak=true}

# Better tables
#+LaTeX_HEADER: \usepackage{booktabs}
#+LaTeX_HEADER: \usepackage[table]{xcolor}
#+LaTeX_HEADER: \usepackage{colortbl}
#+LaTeX_HEADER: \usepackage{siunitx}
#+LaTeX_HEADER: \usepackage{alphabeta}
#+LATEX_HEADER: \definecolor{contiYellow}{RGB}{255,165,0}
#+LATEX_HEADER: \rowcolors[]{2}{contiYellow!5}{contiYellow!20}

#+cite_export: natbib unsrtnat
#+bibliography: refs.bib

#+BEGIN_CENTER
{{{author}}}
#+END_CENTER
#+BEGIN_CENTER
{{{EMAIL}}}
#+END_CENTER
#+BEGIN_CENTER
**HWW**
#+END_CENTER
#+BEGIN_CENTER
/Отдел программно-аппаратных комплексов/
#+END_CENTER
#+BEGIN_CENTER
{{{date}}}
#+END_CENTER

# ===============================================================
# #+LATEX: \definecolor{contiYellow}{RGB}{255,165,0}
# #+LATEX: \rowcolors[]{2}{contiYellow!5}{contiYellow!20}
# #+ATTR_LATEX: :align rS
# | *Type*                                     | *Stiffness* in N/mm |
# |--------------------------------------------+---------------------|
# | Two bearings SKF in radial dir. (supplier) |              167800 |
# | Measurement bearings + rotor + device      |                8398 |
# | Measurement device                         |               14753 |
# | Calculation rotor + bearings               |               19500 |
# |--------------------------------------------+---------------------|
# | Sim. Rotor + "bearings" (package solid)    |               50900 |
# | Sim. Rotor + "joints" (package solid)      |               33320 |
# | Sim. Shaft + "bearings" (without package)  |               12000 |
# | Sim. Shaft + "bearings" 10 mm              |               20030 |
#  #+TBLFM: @2$2=2*8.39e4
# ===============================================================

**Abstract**
\begin{abstract}
The hardware description of the simplifyed version Aleste 520EX — computer, the computer is a replica of the Amstrad CPC 6128.
\end{abstract}

*Keywords*
\begin{keywords}
Amstrad CPC Aleste Concept Retro Computer
\end{keywords}


\clearpage


* Legalese
  :PROPERTIES:
  :COPYING: t
  :UNNUMBERED: notoc
  :END:

  Copyright \copy 2022 valery.hww@gmail.com

\clearpage

#+OPTIONS: toc:nil
#+TOC: headlines:2

\clearpage

* Introduction

The *Xi Aleste*, or *Xi Aleste*  (Russian "Ксай Алеста") is the Amstrad CPC 6128 computer with most of extentions of the Aleste 520EX. The computer developed as open source project for the RetroComputer's comunity.

#+BEGIN_mdframed
Xi is the name of the 14th letter of the Greek alphabet (uppercase Ξ or Ξ, lowercase ξ; Greek: ξι). It is pronounced [ksi] in Modern Greek, and generally /zaɪ/ or /ksaɪ/ in English.[1] In the system of Greek numerals, it has a value of 60.
#+END_mdframed

** Specification

| CPU            |   Z80 20Mhz |
| Graphics modes |  160x200x16 |
|                |   320x200x4 |
|                |   640x200x2 |
|                | 160x200x256 |
|                |  320x200x16 |
|                |   640x200x4 |
|                |             |


* IO Space Address Decoder

#+CAPTION: The CPC Address Decoder
|  <c>  |  <c>  |  <c>  |  <c>  |  <c>  |  <c>  | <c>  | <c>  |   <c>    | <c>  | <c>  | <l>               |
| *A15* | *A14* | *A13* | *A12* | *A11* | *A10* | *A9* | *A8* | *A[7:2]* | *A1* | *A0* | *Register*        |
|-------+-------+-------+-------+-------+-------+------+------+----------+------+------+-------------------|
|  _0_  |  _0_  |       |       |       |       |      |      |          |      |      | Unused^{1}           |
|  _0_  |  _1_  |       |       |       |       |      |      |          |      |      | CPC Gate Array^{2}   |
|       |  _0_  |       |       |       |       |  n   |  n   |    n     |  n   |  n   | Aleste Gate Array |
|       |       |  _0_  |       |       |       |      |      |          |      |      | CPC ROM Bank      |
|       |       |       |  _0_  |       |       |  n   |  n   |    n     |  n   |  n   | CPC LPT           |
|       |       |       |       |  _0_  |       |  n   |  n   |          |      |      | CPC PIO           |
|       |       |       |       |       |  _0_  |      | _0_  |          |      |      | CPC FD Motor      |
|       |       |       |       |       |  _0_  |      | _1_  |          |      |  n   | CPC FDC           |

- Note^{1} The underscore character for the device selection value
- Note^{2} The register selection by D[7:6]

#+BEGIN_mdframed
*N.B.* The underscore character for the address or data line numbers indicates the device will be select by this line.
#+END_mdframed

\clearpage

* The CPC Gate Array Registers

#+CAPTION: [W] The Gate Array Anstrad Hardware Protection Register
#+NAME: tab:cpc_gate_array
| *D7* | *D6* | *Register*                                 |
|------+------+--------------------------------------------|
| _0_  | _0_  | Select Pen                                 |
| _0_  | _1_  | Set pen color                              |
| _1_  | _0_  | Screen mode, ROM enable, Interrupt control |
| _1_  | _1_  | RAM mode                                   |

\clearpage

** PENR - Select Pen

#+CAPTION: [W] PENR Bits
#+NAME: tab:cpc_gate_array_select_pen
|    <r> | <l>           | <l>                           |
| *Bits* | *Name*        | *Description*                 |
|--------+---------------+-------------------------------|
|      5 |               | unused                        |
|      4 | select_border | 1=select border, 0=select pen |
|    3:0 | pen_number    | The pen number                |

\clearpage

** INKR - Select Color for Selected Pen

#+CAPTION: [W] INKR Bits
#+NAME: tab:cpc_gate_array_select_color
|    <r> | <l>              | <l>                                       |
| *Bits* | *Name*           | *Description*                             |
|--------+------------------+-------------------------------------------|
|    7:6 | value 0b01       |                                           |
|      5 |                  | unused                                    |
|    4:0 | cpc_color_number | The CPC color number [[sec:cpc_colors]]       |
|        |                  |                                           |

\clearpage

** RMR - Screen mode, ROM configuration and interrupt control

#+CAPTION: [W] RMR Bits
#+NAME: tab:cpc_gate_array_rmr
|    <r> | <l>        | <l>                                                       |
| *Bits* | *Name*     | *Description*                                             |
|--------+------------+-----------------------------------------------------------|
|    7:6 | value 0b10 |                                                           |
|      5 |            | unused                                                    |
|      4 | reset_irs  | Once wrie 1 it will reset the ISR counter and INT request |
|      3 | upper_rom  | Enable ROM at the upper 16KB page                         |
|      2 | lower_rom  | Enable ROM at the lower 16KB page                         |
|    1:0 | scren_mode | The screen mode [[tab:cpc_screen_modes]]                      |
|        |            |                                                           |

The upper ROM page number in the exact page number of the [[sec:upper_rom_bank][Upper ROM Bank]] register.

\clearpage

#+CAPTION: CPC Screen modes
#+NAME: tab:cpc_screen_modes
| <c> | <c> |   <c>   |
|-----+-----+---------|
|  0  | 16  | 160x200 |
|  1  |  4  | 320x200 |
|  2  |  2  | 640x400 |
|  3  |  4  | 160x200 |

\clearpage

** MMR - RAM Memory Management

#+CAPTION: [W] MRM Bits
#+NAME: tab:cpc_gate_array_mmr
|    <r> | <l>        | <l>                                                    |
| *Bits* | *Name*     | *Description*                                          |
|--------+------------+--------------------------------------------------------|
|    7:6 | value 0b11 |                                                        |
|    5:3 | ram_bank   | The bank number for memory expansion                   |
|    2:0 | ram_config | The one of eight configurations [[tab:ram_configurations]] |

The available RAM configurations listen in the next table.

#+CAPTION: MRM.ram_config
#+NAME: tab:ram_configurations
|           | <c> | <c> | <c> | <c> | <c> | <c> | <c> | <c> |
|           | *0* | *1* | *2* | *3* | *4* | *5* | *6* | *7* |
|-----------+-----+-----+-----+-----+-----+-----+-----+-----|
| 0000-3fff |  0  |  0  | _4_ |  0  |  0  |  0  |  0  |  0  |
| 4000-7fff |  1  |  1  | _5_ |  3  | _4_ | _5_ | _6_ | _7_ |
| 8000-bfff |  2  |  2  | _6_ |  2  |  2  |  2  |  2  |  2  |
| c000-ffff |  3  | _7_ | _7_ | _7_ |  3  |  3  |  3  |  3  |

All underscored pages are expansion of CPC6128, and the actual value will be
computed by the expression:

\begin{equation}
page\_number = config\_pager + ram\_bank * 4;
\end{equation}

Where: \emph{config\_pager} is a value from the table [[tab:ram_configurations]].

\clearpage

** Uppen ROM Bank register
<<sec:upper_rom_bank>>

\clearpage

* CPC Colors
<<sec:cpc_colors>>

#+CAPTION: The CPC colors
#+NAME: tab:cpc_colors
|             <r> | <l>             | <l>            | <l>         |
| Firmware Number | Hardware Number | Colour Name    | Hexadecimal |
|-----------------+-----------------+----------------+-------------|
|               0 | 54h             | Black          | #000000     |
|               1 | 44h (or 50h)    | Blue           | #000080     |
|               2 | 55h             | Bright Blue    | #0000FF     |
|               3 | 5Ch             | Red            | #800000     |
|               4 | 58h             | Magenta        | #800080     |
|               5 | 5Dh             | Mauve          | #8000FF     |
|               6 | 4Ch             | Bright Red     | #FF0000     |
|               7 | 45h (or 48h)    | Purple         | #FF0080     |
|               8 | 4Dh             | Bright Magenta | #FF00FF     |
|               9 | 56h             | Green          | #008000     |
|              10 | 46h             | Cyan           | #008080     |
|              11 | 57h             | Sky Blue       | #0080FF     |
|              12 | 5Eh             | Yellow         | #808000     |
|              13 | 40h (or 41h)    | White          | #808080     |
|              14 | 5Fh             | Pastel Blue    | #8080FF     |
|              15 | 4Eh             | Orange         | #FF8000     |
|              16 | 47h             | Pink           | #FF8080     |
|              17 | 4Fh             | Pastel Magenta | #FF80FF     |
|              18 | 52h             | Bright Green   | #00FF00     |
|              19 | 42h (or 51h)    | Sea Green      | #00FF80     |
|              20 | 53h             | Bright Cyan    | #00FFFF     |
|              21 | 5Ah             | Lime           | #80FF00     |
|              22 | 59h             | Pastel Green   | #80FF80     |
|              23 | 5Bh             | Pastel Cyan    | #80FFFF     |
|              24 | 4Ah             | Bright Yellow  | #FFFF00     |
|              25 | 43h (or 49h)    | Pastel Yellow  | #FFFF80     |
|              26 | 4Bh             | Bright White   | #FFFFFF     |

\clearpage

* The CPC interrupt generator

There is an internal 6-bit counter \emph{hsync\_cnt} which gets incremented on
each \emph{hsync} signal. When it equals 52, the counter is set to zero. Since a
frame is composed of 312 \emph{hsync}s, at most 6 interrupts are generated per
frame.

When the CPU write bit 4 of RMR command is set, it will clear the
\emph{hsync\_cnt} and the interrupt request. The operation is not persistant, and
the counter will continue it's increments every \emph{hsync}.

After a \emph{vsync} signal is "sensed", there will be two more \emph{hsync}s
and \emph{hsync\_cnt} will reset to zero. The \emph{C} equivalent of the
\emph{hsync\_cnt} is in the appendix [[sec:interrupt_generator_c]].

\clearpage

* The Interrupt Syncing

Typically a \emph{halt} instruction after polling \emph{vsync} bit of \emph{PORT B} will allow synchronizing to the frame:

#+BEGIN_SRC asm
        ld      b, #f5          ; Port B of 8255
vsync1  in      a, (c)
        rra                     ; Push bit 0 into carry
        jr      nc, vsync1      ; Carry is set when VSYNC is active

        halt                    ; Synchronized to the beam
#+END_SRC

The reason \emph{halt} is needed for a perfect synchronization is because the
code for polling \emph{vsync} will have a variable clock cycle difference at the
end. If no \emph{halt} is issued, it is not possible to know exactly where the
Z80 code is synced with respect to the screen.

The next interrupt is generated two \emph{hsync} after a \emph{vsync}, which
means when the Z80 wakes up after a \emph{halt} it will have slept for ~128
microseconds in addition to the amount of time spent in the interrupt routine.

* Xi Aleste Extentions

This chapter contans the extentions of the CPC platform, most of those
extensions were implemented in the Aleste 520EX, but some of them was not.

** The Xi Aleste Address Decoder

The Aleste address decoder works only if \emph{aleste_mode} is in the
\emph{hardware_control} register.

#+CAPTION: [R/W] The Aleste Address Decoder
|  <c>  |  <c>  |    <c>    | <c>  | <c>  | <c>  | <c>  | <c>  | <c>  | <c>  | <c>  | <l>               |
| *A15* | *A14* | *A[13:8]* | *A7* | *A6* | *A5* | *A4* | *A3* | *A2* | *A1* | *A0* | *Register*        |
|-------+-------+-----------+------+------+------+------+------+------+------+------+-------------------|
|       |  _0_  |           | _1_  | _1_  | _1_  |  0   |      |      |      |      | Unused            |
|       |  _0_  |           | _1_  | _1_  | _1_  |  1   |      |      |  n^{1}  |  n^{1}  | CPC Gate Array    |
|       |  _0_  |           | _1_  | _1_  | _0_  |  n   |  n   |  n   |  n   |  n   | Aleste Gate Array |
|       |  _0_  |           | _1_  | _0_  | _1_  |      |      |      |      |      | CPC ROM Bank      |
|       |  _0_  |           | _1_  | _0_  | _0_  |  n   |  n   |  n   |  n   |  n   | Aleste Expansion^{4} |
|       |  _0_  |           | _0_  | _1_  | _1_  |      |      |      |  n^{2}  |  n^{2}  | CPC PIO           |
|       |  _0_  |           | _0_  | _1_  | _0_  |      |      |      |  0^{3}  |      | CPC FD Motor      |
|       |  _0_  |           | _0_  | _0_  | _1_  |      |      |      |  1^{3}  |  n   | CPC FDC           |

- Note^{1} The CPC select the register by D[7:6], but in the Xi Aleste it uses A[1:0]
- Note^{2} The CPC select the PIO register by A[9:8], but in the Xi Aleste it uses A[1:0]
- Note^{3} The CPC select the register by A[8], but in the Xi Aleste it uses A[1]
- Note^{4} The Xi Aleste uses this space, as the external IO space.

** The Xi Aleste Registers


#+CAPTION: Xi Aleste Screen modes
#+NAME: tab:cpc_screen_modes
| <c> | <c> |   <c>   |
|-----+-----+---------|
|  0  | 256 | 160x200 |
|  1  | 16  | 320x200 |
|  2  |  4  | 640x400 |
|  3  | 16  | 160x200 |


#+CAPTION: Aleste 8-bits Colors
#+NAME: tab:aleste_colors
| *Bits* | *Name* | *Description* |
|--------+--------+---------------|
|    7:5 | r      | Red value     |
|    4:2 | g      | Green value   |
|    1:0 | b      | Blue value    |


#+CAPTION: [R/W] The Gate Array Anstrad Hardware Protection Register
| *A7* | *A6* | *A5* | *A4* | *A3* | *A2* | *A1* | *A0* | *Register*                                 |
|------+------+------+------+------+------+------+------+--------------------------------------------|
|      |      |      | (0)  |      |      | (0)  | (0)  | Select Pen                                 |
|      |      |      | (0)  |      |      | (0)  | (1)  | Set pen color                              |
|      |      |      | (0)  |      |      | (1)  | (0)  | Screen mode, ROM enable, Interrupt control |
|      |      |      | (0)  |      |      | (1)  | (0)  | RAM mode                                   |

The hardware protection register enables and disables some of hardware features. When the CPU access to
a disabled feature -- the error signal will be generated. The error signal invoke the NMI interrupt.

#+CAPTION: [R/W] The printer port data
#+NAME: tab:printer_data
|    <r> | <l>          | <l>                                  |
| *Bits* | *Name*       | *Description*                        |
|--------+--------------+--------------------------------------|
|    7:0 | printer_data | Write or read the printer's data bus |

#+CAPTION: [R/W] The printer direction
#+NAME: tab:printer_direction
|    <r> | <l>          | <l>                                  |
| *Bits* | *Name*       | *Description*                        |
|--------+--------------+--------------------------------------|
|    7:0 | printer_data | Write or read the printer's data bus |


#+CAPTION: [R/W] Hardware Protection Register
#+NAME: tab:hardware_protection_register
|    <r> | <l>     | <l>                                  |
| *Bits* | *Name*  | *Description*                        |
|--------+---------+--------------------------------------|
|    7:0 | hwprot_ | Write or read the printer's data bus |

#+CAPTION: [R/W] Hardware Error Register
#+NAME: tab:hardware_error_register
#+ATTR_LATEX: :align left
|    <r> | <l>      | <l>                                  |
| *Bits* | *Name*   | *Description*                        |
|--------+----------+--------------------------------------|
|    7:0 | hw_prot_ | Write or read the printer's data bus |

[cite:@hzeller]
\clearpage

#+print_bibliography:

The CPC connector expanded on the lest significant pin numbers. There are 2x6 pins added.

#+CAPTION: CPC Expansion Connector
#+NAME: tab:cpc_expansion
#+ATTR_LATEX: :align left

| <r> | <l>  | <l>       | <r> | <l> | <l>       |
|-----+------+-----------+-----+-----+-----------|
|   1 | AIN  | SND_L     |   2 | AIN | SND_R     |
|   3 | PWR  | AGND/*SL0 |   4 | PWR | AGND/*SL0 |
|   5 | BI   | A17       |   6 | BI  | A16       |
|   7 | BI   | A19       |   8 | BI  | A18       |
|   9 | BI   | A21       |  10 | BI  | A20       |
|  11 | BI   | A23       |  12 | BI  | A22       |
|-----+------+-----------+-----+-----+-----------|
|  13 | AOUT | Sound     |  12 | PWR | GND       |
|  15 | BI   | A15       |  14 | BI  | A14       |
|  17 | BI   | A13       |  16 | BI  | A12       |
|  19 | BI   | A11       |  18 | BI  | A10       |
|  21 | BI   | A9        |  22 | BI  | A8        |
|  23 | BI   | A7        |  24 | BI  | A6        |
|  25 | BI   | A5        |  26 | BI  | A4        |
|  27 | BI   | A3        |  28 | BI  | A2        |
|  29 | BI   | A1        |  30 | BI  | A0        |
|  31 | BI   | D7        |  32 | BI  | D6        |
|  33 | BI   | D5        |  34 | BI  | D4        |
|-----+------+-----------+-----+-----+-----------|
|  35 | BI   | D3        |  36 | BI  | D2        |
|  37 | PWR  | VCC       |  38 |     | *MREQ     |
|  39 | BI   | D1        |  40 | BI  | D0        |
|  41 |      | *M1       |  42 |     | *RFSH     |
|  43 |      | *IORQ     |  44 |     | *RD       |
|  45 |      | *WR       |  46 |     | *HALT     |
|  47 |      | *INT      |  48 |     | *NMI      |
|  49 |      | *BUSRQ    |  50 |     | *BUSAK    |
|  51 |      | READY     |  52 |     | *BRST     |
|  53 |      | *RESET    |  54 |     | *ROMEN    |
|  55 |      | ROMDIS    |  56 |     | *RAMRD    |
|  57 |      | RAMDIS    |  58 | OUT | CURSOR    |
|  59 |      | LPEN      |  60 | IN  | *EXP      |
|  61 | PWR  | GND       |  62 | OUT | CLK4      |



The pins *SL[0:1] have two different functions, it is a analog ground and the slot number. The slot 0 used for on the boards space.

| *SL1 *SL0] | Descritopm |
|  1 0       | Slot 1 |
|  0 1       | Slot 2 |
|  0 0       | Slot 3 |

* Appendix

** The CPC  Interrups Generator Code
<<sec:interrupt_generator_c>>

#+BEGIN_SRC C
/*
  Indicates that a new hsync pulse has been recognised. This should be
  supplied on the falling edge of the CRTC HSYNC signal, which is the
  trailing edge because it is active high.
*/
inline void signal_hsync() {
    // Increment the timer and if it has hit 52 then reset it and
    // set the interrupt request line to true.
     ++timer_;
    if(timer_ == 52) {
        timer_ = 0;
        interrupt_request_ = true;
    }

    // If a vertical sync has previously been indicated then after two
    // further horizontal syncs the timer should either (i) set the interrupt
    // line, if bit 4 is clear; or (ii) reset the timer.
    if(reset_counter_) {
        --reset_counter_;
        if(!reset_counter_) {
            if(timer_ & 32) {
                interrupt_request_ = true;
            }
            timer_ = 0;
        }
    }
}
/// Indicates the leading edge of a new vertical sync.
inline void signal_vsync() {
    reset_counter_ = 2;
}
/// Indicates that an interrupt acknowledge has been received from the Z80.
inline void signal_interrupt_acknowledge() {
    interrupt_request_ = false;
    timer_ &= ~32;
}
/// @returns @c true if an interrupt is currently requested; @c false otherwise.
inline bool get_request() {
    return last_interrupt_request_ = interrupt_request_;
}
/// Asks whether the interrupt status has changed.
inline bool request_has_changed() {
    return last_interrupt_request_ != interrupt_request_;
}
/// Resets the timer.
inline void reset_count() {
    timer_ = 0;
    interrupt_request_ = false;
}
/// Writing to the RMR register
inline void write_to_rmr(uint8_t val) {
    // Reset the interrupt timer if requested.
    if(value & 0x10) {
        timer_ = 0;
        interrupt_request_ = false;
    }
}
#+END_SRC


#+HEADER: :file ../img/contour.svg :imagemagick yes
#+HEADER: :results output silent :headers '("\\usepackage{tikz}")
#+HEADER: :fit yes :imoutoptions -geometry 400 :iminoptions -density 600



#+BEGIN_src latex
\begin{tikztimingtable}
Name & hLLLLh \\
Clock & 10{c} \\
Signal & z4D{Text}z \\
\end{tikztimingtable}
#+END_src


{signal: [
  {name: '32 MHz', wave: 'n.............'},
  {name: 'cck', wave: '10...1...0...1'},
  {name: 'cckq', wave: '1..0...1...0..'},
  {name: '8 MHz', wave: '10.1.0.1.0.1.0'},
  {name: '8 MHz', wave: '0.1.0.1.0.1.0.'},
]}
