---
title: DialogWindow
---

## Description

Field module of Final Fantasy VII han handle 4 dialog windows at once. All data for them stored in structures with size 0x30. There are a lot of additional arrays used for variables offsets and many other data used by windows.

All windows can be called any time with 0x40 MESSAGE or 0x48 ASK opcodes. They will be called all the time, when window is shown on screen. Only after window is closed this opcode will be skipped and script pointer go to next opcode. There are a lot of window parameters opcode. they are set different parameters to window. They are 0x2E WCLS 0x2F WSIZW 0x36 WSPCL 0x37 WNUMB 0x38 STTIM 0x41 MPARA 0x42 MPRA2 0x50 WINDOW 0x51 WMOVE 0x52 WMODE 0x53 WREST 0x55 WROW 0x57 SWCOL. All of them write parameters directly to structure, but they need to be called from the same entity else they wait till the window is closed.

Window is state driven. The way how window handling input and shown are dependent on state. State handling called by MESSAGE and ASK opcode. State handling of this two opcodes a bit different. By default window state is zero (closed window).

## Memory Layout

This is offsets to english version of game. But japanese has exactly same structure, just offsets a bit different. this is all offsets used by dialog windows (opcode reading offsets not included here).

800491D0 character id -\> savemap block assosiation id. (0-0, 1-1, 2-2, 3-3, 4-4, 5-5, 6-6, 7-7, 8-8, 9-6, A-7)

80049208 window RGB. (size \[r\]\[g\]\[b\]x4)

80071E2C number of opened windows. Increment this during window initialization. Decrement during closing.

8007E7A8 offset to dialog block.

8008326C \[\] windows parent entity (size 0x01).

80083274 \[\]\[\]\[\]\[\] windows data (size 0x30). Pointer to dialog string.

80083278 \[\]\[\] windows data (size 0x30). WINDOW x.

8008327A \[\]\[\] windows data (size 0x30). WINDOW y.

8008327C \[\]\[\] windows data (size 0x30). WINDOW width.

8008327E \[\]\[\] windows data (size 0x30). WINDOW height.

80083280 \[\]\[\] windows data (size 0x30). WINDOW current width. Set during dialog init (width / 4) (8 - min value).

80083282 \[\]\[\] windows data (size 0x30). WINDOW current height. Set during dialog init (height / 4) (8 - min value).

80083284 \[\]\[\] windows data (size 0x30). Text scrolling value.

80083286 \[\]\[\] windows data (size 0x30). Letters in window string.

80083288 \[\]\[\] windows data (size 0x30). Bytes in window string.

8008328A \[\]\[\] windows data (size 0x30). Current row in string. Incremented during copy.

8008328D \[\] windows data (size 0x30). WMODE style.

8008328E \[\] windows data (size 0x30). Show pointer.

8008328F \[\] windows data (size 0x30). WSPCL type.

80083291 \[\] windows data (size 0x30). WNUMB number of digits in number.

80083294 \[\]\[\]\[\]\[\] windows data (size 0x30). WNUMB number.

80083298 \[\]\[\] windows data (size 0x30). Pointer X.

8008329A \[\]\[\] windows data (size 0x30). Pointer Y.

8008329C \[\] windows data (size 0x30). WSPCL x.

8008329E \[\] windows data (size 0x30). WSPCL y.

800832A0 \[\]\[\] windows data (size 0x30). window state.

800832A2 \[\] windows data (size 0x30). WMODE cbc.

8009C6E4 savemap start

8009C748 savemap character name data (Cloud).

8009D268 \[\]\[\]\[\]\[\] savemap seconds for timer (set in STTIM).

8009D288 savemap memory bank 1/2 start

8009D391 \[\]\[\]\[\] savemap party info.

8009D7D0 \[\] savemap message speed.

800E0738 digit-\>letter assosiation array (10-26 letters from character table)

800E4214 memory bank array for getting variable for windows. 8 for each window. (size 0x8 0x1)

800E4234 reading offsets in dialog in field. One for each window. (size 0x4).

800E424C total window height. Height of window with extra rows. Set this during B state in window as 8008328A (current row) \* 10 + 11. (size 0x2).

800E4278 \[\]\[\] name reading offset for dialog windows. (size 0x2).

800E4280 \[\]\[\] current variable for windows. (size 0x2).

800E4944 text for windows to diaplay. (size 0x100)

800E4D48 offsets for getting variable from memory block for windows. 8 for each window. (size 0x10 0x2)

80114278 \[\]\[\] some data for windows. Set this to zero during window initialization

801142CC \[\]\[\] additional rows during text scrolling. (size 0x2).

8011445C \[\]\[\] time to wait for windows. (size 0x2).

80114470 \[\]\[\] field dialog value which content value how mush characters add at current character copy. (size 0x08 0x2).

80114480 \[\]\[\] field dialog OK button speed modificator. (size 0x08 0x2).

801144DC \[\]\[\] temp variable string. First we write variable here and then copy it to window string. (size 0x10).

8011451C \[\]\[\] current variable read offset. (size 0x2).

## State

0 - init window.

1 - window appears.

2 - text appears.

3 - pause string output. (next part of sting will be added to outputed string after time).

4 - wait for next row (next row of text will show after OK button).

5

6 - window completly shown (window will close after OK button press).

7 - window closing.

8 - scroll to next row.

9 - init next window.

A

B - pause string output. (text will be scrolled to next window while you hold OK button).

C - scroll to next row during OK button press.

D - pause string output. (next part of sting will be added to outputed string after OK button).

E - wait for next window (next block of text will show after this). Opcodes 0xE8 0xE9.

message window states:

state 0: call "dialog_window_init".

state 1: call "dialog_window_growth".

state 2: call "dialog_copy_text_from_field".

state 3: discrease 8011445C (wait time) by 1 each call. When wait time == 0, then set state to 2.

state 4: if "OK" button (0x0020) pressed then discrease 80083284 (dialog scrolling value) by 2. Increment 801142CC (additional rows) by 1. Set state to 8.

state 6: if WMODE cbc & 0x01 not set and "OK" button (0x0020) pressed then set state to 7 and call "dialog_window_discrease".

state 7: call "dialog_window_discrease".

state 8: call "dialog_scroll_text".

state 9: call "dialog_init_next_window".

state B: if "OK" button (0x0020) pressed then set state to C. Discrease 80083284 (dialog scrolling value) by 2. Set 800E424C to current_row \* 10 + 11.

state C: call "dialog_scroll_text_during_ok".

state D: if "OK" button (0x0020) pressed then set state to 2.

state E: if "OK" button (0x0020) pressed then call "dialog_init_next_window".

ask window states:

state 0: call "dialog_window_init".

state 1: call "dialog_window_growth".

state 2: call "dialog_copy_text_from_field".

state 3: discrease 8011445C (wait time) by 1 each call. When wait time == 0, then set state to 2.

state 4: if "OK" button (0x0020) pressed then discrease 80083284 (dialog scrolling value) by 2. Increment 801142CC (additional rows) by 1. Set state to 8.

state 6: if WMODE cbc & 0x01 not set then set (8008328E) enable pointer to 1.

`        if WMODE cbc & 0x01 not set and "UP" button (0x1000) pressed and first row less then current row then discrease current row by 1 and call "play_window_pointer_click_sound".`  
`        if WMODE cbc & 0x01 not set and "DOWN" button (0x4000) pressed and currewnt row less then last row then increase current row by 1 and call "play_window_pointer_click_sound".`  
`        if WMODE cbc & 0x01 not set then set (80083298) pointex X to 5 and (8008329A) pointer Y to current_row * 10 + 6.`  
`        if WMODE cbc & 0x01 not set and "OK" button (0x0020) pressed then set state to 7 and call "play_window_pointer_click_sound" and "dialog_window_discrease".`

state 7: call "dialog_window_discrease".

`        if window is still closing invert (8008328E) enable pointer to make it blink.`  
`        when window closed set (8008328E) enable pointer to 0.`

state 8: call "dialog_scroll_text".

state 9: call "dialog_init_next_window".

state B: if "OK" button (0x0020) pressed then set state to C. Discrease 80083284 (dialog scrolling value) by 2. Set 800E424C to current_row \* 10 + 11.

state C: call "dialog_scroll_text_during_ok".

state D: if "OK" button (0x0020) pressed then set state to 2.

state E: if "OK" button (0x0020) pressed then call "dialog_init_next_window".

state cycle 0-\>1-\>2

2-\>0xE7-\>4-\>8-\>2

2-\>0xE8-\>E-\>2

2-\>0xE9-\>E-\>2

2-\>0xFEDC-\>D-\>2

2-\>0xFEDD-\>3-\>2

2-\>0xFEE0-\>B-\>C-\>9-\>2

2-\>0xFEFF-\>D-\>2

2-\>0xFF-\>6-\>7-\>0

## Functions

dialog_copy_text_from_field:

`   First we set reading parameters.`  
`   If 800832A2 (cbc) 0x02 bit is set then A1 = 0x100, S5 = 1. This will cause all characters show at once.`  
`   If 800832A2 (cbc) 0x02 bit is not set then we check if "OK" button (0x0020) pressed. If yes we increment 80114480 by 1 (but no more than 80), if not - decrement by 1 (no less than 1).`  
`   If 800832A2 (cbc) 0x02 bit is not set and message speed < 0x80 then we set A1 = ((80 - message speed) / 32) + 2. S5 = 1.`  
`   If 800832A2 (cbc) 0x02 bit is not set and message speed >= 0x80 then we set A1 = 2. S5 = ((message speed - 80) / 32) + 1.`  
`   Now we use A1, S5, 80114480 and 80114470 to calculate 80114470 value which will be used to calculate if we must continue reading characters or not. 80114470 = 80114470 + S5 * 80114480 / 10 + A1.`  
`   Now we will read characters until 80114470 <= S5 if so - end string copy until next call.`

dialog_window_init:

`   Do check if pointer to field dialog section not 0 (call "funcd4848" if it zero and return 1 (stop MESSAGE or ASK opcode)).`  
`   Set parent entity to this window.`  
`   Set current_width to width / 4, but no less than 8.`  
`   Set current_height to height / 4, but no less than 8.`  
`   Set 80083274 to pointer to window string.`  
`   Set 80083284 to 0.`  
`   Set 80083286 to 0 (letters in window string).`  
`   Set 80083288 to 0 (bytes in window string).`  
`   Set 8008328A to 0.`  
`   Set 8008328E to 0.`  
`   Set state to 1.`  
`   Set 800E4234 as pointer to field string.`  
`   Set 80114278 to 0 (name reading offset).`  
`   Set 80114280 to 0 (current variable).`  
`   Set 801142CC to 0.`  
`   Set 80114470 to 0.`  
`   Set 80114480 to 1.`  
`   Set 8011451C to -1 (current variable reading offset).`  
`   Increment 80071E2C (opened windows number) by 1;`  
`   Write 0xFF at start of window string.`

dialog_window_growth:

`   Increment current_width by width / 4, but current_width no less than 8 and no more than width.`  
`   Increment current_height by height / 4, but current_height no less than 8 and no more than height.`  
`   If current_width == width and current_height == height then set state to 1.`

dialog_window_discrease:

`   Decrement current_width by width / 4, but current_width no less than 8.`  
`   Decrement current_height by height / 4, but current_height no less than 8.`  
`   If current_width  <= 8 and current_height <= 8 then set state to 0. Set 80083286 (letters in window string) to 0. Remove parent entity from this window (set to FF).`  
`   Decrement 80071E2C (opened windows number) by 1;`

dialog_init_next_window:

`   Set 80083284 to 0.`  
`   Set 80083286 to 0.`  
`   Set 80083288 to 0.`  
`   Set 8008328A to 0.`  
`   Set 800832A0 to 2 (state).`  
`   Set 801142CC to 0.`  
`   Set 80114480 to 1.`  
`   Write 0xFF at start of window string.`

dialog_copy_text_from_field:

`   Copy text from field to window string. Set state to 6 when tork normally. Can set other state according to readed opcodes.`

dialog_scroll_text:

`   Discrease 80083284 (dialog scrolling value) by 2 each time called.`  
`   If discreased by 0x10 (X & 0x0F == false) - set state to 2 and copy new part of string.`

dialog_scroll_text_during_ok:

`   Discrease 80083284 (dialog scrolling value) by 80114480 / 4 each time called and "OK" button (0x0020) pressed.`  
`   Increase 80114480 by 1 each time "OK" button (0x0020) pressed.`  
`   Discrease 80114480 by 1 each time "OK" button (0x0020) not pressed.`  
`   If 800E424C (total width value) + 80083284 (text scrolling value) less than zero set state to 9.`

set_window_x_y_width_height:

`   Writes data to window structure.`  
`   Set x_position to given x. X no less than 8. If x + width > 0x138 then set x to 138 - width.`  
`   Set y_position to given y. Y no less than 8. If y + height > 0xE0 then set x to E0 - height.`  
`   Set width to given width.`  
`   Set height to given height.`

set_window_style_cbc:

`   Writes data to window structure.`  
`   Set WMODE_style to given stype.`  
`   Set WMODE_cbc to given cbc.`

set_state_to_close:

`   If state == 1 return 0;`  
`   If state 0 or 3 or 5 or 7 or 9 or A or C or E return 1;`  
`   Id state 2 or 4 or 6 or 8 or B or D set state to 7 and then return 1;`

add_x_y_to_window_position:

`   Writes data to window structure.`  
`   Add given x to x_position.`  
`   Add given y to y_position.`

reset_window:

`   Writes data to window structure.`  
`   Set x_position to 0x08.`  
`   Set y_position to 0x08 if we reset window with id == 1, set to 0x95 otherwise.`  
`   Set width to 0x139;`  
`   Set height to 0x49;`  
`   Set current_width to 1;`  
`   Set current_height to 1;`  
`   Set state to 0;`  
`   Set WMODE_style to 0;`  
`   Set WSPCL_type to 0.`  
`   Set 80083290 to 0.`  
`   Set WNUMB number of digits in number to 6.`  
`   Set WSPCL_x to 0.`  
`   Set WSPCL_y to 0.`  
`   Set WMODE_cbc to 0.`  
`   Set windows parent entity to -1 (0xFF).`  
`   Clear 800E4214 (memory bank array for getting variable for windows) array with 0.`  
`   Clear 800E4D48 (offsets for getting variable from memory block for windows) array with 0.`  
`   Set 8011445C (time to wait for windows) to 0.`

set_window_height:

`   Writes data to window structure.`  
`   Set height to given height value.`

## Opcodes

0x2E WCLS

`   [0x2E][window_id]`  
`   If window opened call "set_state_to_close" and then "manage_window_states" to close window.`  
`   This opcode called until window closed (we can call this opcode from any entity).`

0x2F WSIZW

`   [0x2F][window_id][ [x] ][ [y] ][ [width] ][ [height] ]`  
`   If window not opened yet - call "WINDOW" opcode which set parameters and continue script execution.`  
`   If window opened and parent entity the same as current entity call "set_state_to_close" and then "manage_window_states" to close window.`  
`   This opcode called until window closed (if we try to call this opcode from other entity this opcode will wait until window closed).`

0x36 WSPCL

`   [0x36][window_id][type][x][y]`  
`   Writes type to WSPCL_type in window structure.`  
`   Writes x to WSPCL_x in window structure.`  
`   Writes y to WSPCL_y in window structure.`  
`   In field files only type 00 01 and 02 used.`

0x37 WNUMB

`   [0x37][bank_for_number_lower_part/bank_for_number_upper_part][window_id][ [number_lower_part] ][ [number_upper_part] ][number_of_digits]`  
`   Writes number to WNUMB_number in window structure (construct from upper and lower parts).`  
`   Writes number_of_digits to WNUMB_number_of_digits in window structure.`

0x38 STTIM

`   [0x38][bank_for_hours/bank_for_minutes][0/bank_for_seconds][hours][minutes][seconds]`  
`   Writes seconds value to 8009D268 (savemap) as hours * 3600 + minutes * 60 + seconds.`

0x40 MESSAGE

`   [0x40][window_id][dialog_id]`  
`   Call "manage_window_states" which sets window_id and dialog_id, handle window states and do many other things.`  
`   This opcode called until window closed.`

0x41 MPARA

`   [0x41][bank_for_variable_id/bank_for_value][window_id][variable_id][value]`  
`   Writes bank_for_value to 800E4214 memory bank variable array (size 0x08 0x01). This value will be used when we will copy field dialog to window string.`  
`   Writes value to 800E4D48 offsets for variable array (size 0x10 0x02). This value will be used when we will copy field dialog to window string.`

0x42 MPRA2

`   [0x42][bank_for_variable_id/bank_for_value][window_id][variable_id][ [value] ]`  
`   Writes bank_for_value to 800E4214 memory bank variable array (size 0x08 0x01). This value will be used when we will copy field dialog to window string.`  
`   Writes value to 800E4D48 offsets for variable array (size 0x10 0x02). This value will be used when we will copy field dialog to window string.`

0x48 ASK

`   [0x48][0/bank_for_pointer_row][window_id][dialog_id][first][last][offset_for_pointer_row]`  
`   Call "manage_ask_window_states" which sets window_id and dialog_id, handle ask window states and do many other things.`  
`   Stores current pointer row to given bank_for_pointer_row + offset_for_pointer_row.`  
`   Set 8009ABF4 + 32 (UC) to game data.`  
`   This opcode called until window closed.`  
`   When window closed restore character movement from 80081DC4 (current UP opcode state).`

0x50 WINDOW

`   [0x50][window_id][ [x] ][ [y] ][ [width] ][ [height] ]`  
`   Call "set_window_x_y_width_height" which sets parameters to window structure.`

0x51 WMOVE

`   [0x51][window_id][ [x] ][ [y] ]`  
`   Call "add_x_y_to_window_position" which adds x and y to current values in window structure.`

0x52 WMODE

`   [0x52][window_id][style][cbc]`  
`   Call "set_window_style_cbc" which sets  parameters to window structure.`  
`   In field files only cbc 00 and 01 used. But there are check on 02 during "dialog_copy_text_from_field" function.`  
`   In field files only style 00, 01 and 02 used.`

0x53 WREST

`   [0x53][window_id]`  
`   Call "reset_window" which sets default values to window structure and some additional arrays.`

0x54 WCLSE

`   [0x54][window_id]`  
`   Call "set_state_to_close" and if it returns 1 - continue script execution.`  
`   Opcode tries to set window to close. if it can't - continue execution. this opcode will called again only if window is opening (state 1). It will wait till it completly opened.`  
`   Window will not actually closed by itself. MESSAGE or ASK opcode must handle actual process.`

0x55 WROW

`   [0x55][window_id][rows]`  
`   Call "set_window_height" with height value as (rows * 0x10) + 9`

0x56 GWCOL

`   [0x56][bank_for_corner/bank_for_r][bank_for_g/bank_for_b][corner][r][g][b]`  
`   Store 80049208 (red value of given corner) in bank_for_r + r.`  
`   Store 80049209 (green value of given corner) in bank_for_g + g.`  
`   Store 8004920A (blue value of given corner) in bank_for_b + b.`

0x57 SWCOL

`   [0x57][bank_for_corner/bank_for_r][bank_for_g/bank_for_b][corner][r][g][b]`  
`   Set 80049208 (red value of given corner) to bank_for_r + r.`  
`   Set 80049209 (green value of given corner) to bank_for_g + g.`  
`   Set 8004920A (blue value of given corner) in bank_for_b + b.`

## Special Letters

field copy:

XX - for any usual letter we inctement letter and byte data by 1.

E0 - tabulation. Writes 0xA spaces(0x00) to string data. Increment letters and bytes data by 0xA.

E1 - tabulation. Writes 0x4 spaces(0x00) to string data. Increment letters and bytes data by 0x4.

E2 - writes 0x0C, 0x00(, ) Increment letters and bytes data by 0x2.

E3 - writes 0x0E, 0x02(.") Increment letters and bytes data by 0x2.

E4 - writes 0xA9, 0x02(...") Increment letters and bytes data by 0x2.

E5 - usual letter.

E6 - usual letter.

E7 - next row.

`       If row number equal to max row number - write FF to string. Set window state to 0x4. Write 0 to 80114470 array. Write 1 to 80114480 array.`  
`       If row numbet less then max row number - write E7 to string. Increment byte and row data by 1.`

E8 - next window. Set window state to 0xE. End string copying. Write FF to string. Write 0x0000 to 80114470 array. Write 0x0001 to 80114480 array.

E9 - next window. Set window state to 0xE. End string copying. Write FF to string. Write 0x0000 to 80114470 array. Write 0x0001 to 80114480 array.

EA - character name from savemap 0x00 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

EB - character name from savemap 0x01 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

EC - character name from savemap 0x02 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

ED - character name from savemap 0x03 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

EE - character name from savemap 0x04 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

EF - character name from savemap 0x05 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F0 - character name from savemap 0x06 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F1 - character name from savemap 0x07 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F2 - character name from savemap 0x08 character data. Copy 9 bytes or until encount FF. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F3 - party character name. Get character id from 0x0CAE party data in savemap and load character name from savemap data. Copy 9 bytes or until encount FF. If party character 0xFF (none) - write 0xA9(...) 9 times. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F4 - party character name. Get character id from 0x0CAF party data in savemap and load character name from savemap data. Copy 9 bytes or until encount FF. If party character 0xFF (none) - write 0xA9(...) 9 times. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F5 - party character name. Get character id from 0x0CB0 party data in savemap and load character name from savemap data. Copy 9 bytes or until encount FF. If party character 0xFF (none) - write 0xA9(...) 9 times. Increment letters and byte data by 1 for each copying byte. Descrease 80114470 by S5 for each copying byte.

F6 - usual letter.

F7 - usual letter.

F8 - usual letter.

F9 - usual letter.

FA - copied to string together with next byte. Increment letters data by 1 and byte data by 2. Used in the Japanese version to print special characters from the extended font. The sequence 0xFA00 translates to screen as '必'.

FB - copied to string together with next byte. Increment letters data by 1 and byte data by 2. Used in the Japanese version to print special characters from the extended font. The sequence 0xFB00 translates to screen as '安'.

FC - copied to string together with next byte. Increment letters data by 1 and byte data by 2. Used in the Japanese version to print special characters from the extended font. The sequence 0xFC00 translates to screen as '由'.

FD - copied to string together with next byte. Increment letters data by 1 and byte data by 2. Used in the Japanese version to print special characters from the extended font. The sequence 0xFD00 translates to screen as '友'.

FE - special opcode. Write FE to string. Increment byte data by 1. Read next byte and do something according to it. Case 00-D1 are used in the Japanese version to print special characters from the extended font. The sequence 0xFE00 translates to screen as '孫'.

FED2 - increment byte data by 1. Write this byte to string. (colour formater: Gray)

FED3 - increment byte data by 1. Write this byte to string. (colour formater: Blue)

FED4 - increment byte data by 1. Write this byte to string. (colour formater: Red)

FED5 - increment byte data by 1. Write this byte to string. (colour formater: Purple)

FED6 - increment byte data by 1. Write this byte to string. (colour formater: Green)

FED7 - increment byte data by 1. Write this byte to string. (colour formater: Cyan)

FED8 - increment byte data by 1. Write this byte to string. (colour formater: Yellow)

FED9 - increment byte data by 1. Write this byte to string. (colour formater: White)

FEDA - increment byte data by 1. Write this byte to string. (colour formater: Flash)

FEDB - increment byte data by 1. Write this byte to string. (colour formater: Rainbow)

FEDC - remove FE from string by decrement byte data by 1. Set window state to 0xD. Write 0x0000 to 80114470 array. Write 0x0001 to 80114480 array. End string copying. Write FF to string.

FEDD - set window state to 0x3. Copy next byte and byte after it to 8011445C array (swapped). Increment byte data by 3 (similar to write 000000). Write FF to string. End string copying.

FEDE - remove FE from string by decrement byte data by 1. Get 1 or 2 byte value from memory bank. Convert that digit to string without preceding zeros (with ending FF). Copy 0x10 bytes (or until we met FF) of given string to window string incrementing byte and letter data by 1 for each byte. Descrease 80114470 by S5 for each copying byte. There can be 8 variable in one window (for all variable opcodes DE DF E1).

FEDF - remove FE from string by decrement byte data by 1. Get 1 or 2 byte value from memory bank. Convert that digit to string as hex without preceding zeros (with ending FF). Copy 0x10 bytes (or until we met FF) of given string to window string incrementing byte and letter data by 1 for each byte. Descrease 80114470 by S5 for each copying byte. There can be 8 variable in one window (for all variable opcodes DE DF E1).

FEE0 - remove FE from string by decrement byte data by 1. Set window state to 0xB. Write 0x0000 to 80114470 array. Write 0x0001 to 80114480 array. End string copying. Write FF to string.

FEE1 - remove FE from string by decrement byte data by 1. Get 1 or 2 byte value from memory bank. Convert that digit to string replacing preceding zeros with space (0x00) (with ending FF). Copy 0x10 bytes (or until we met FF) of given string to window string incrementing byte and letter data by 1 for each byte. Descrease 80114470 by S5 for each copying byte. There can be 8 variable in one window (for all variable opcodes DE DF E1).

FEE2 - remove FE from string by decrement byte data by 1. Get 3-4 byte as offset and 5-6 byte as length. Copy length bytes from memory block 1/2 offset + offset to temp string (end string with 0xFF). Copy bytes from temp string to winfow string until we met FF, incrementing byte and letter data by 1 for each byte. Descrease 80114470 by S5 for each copying byte.

FEE3 - usual letter.

FEE4 - usual letter.

FEE5 - usual letter.

FEE6 - usual letter.

FEE7 - usual letter.

FEE8 - usual letter.

FEE9 - increment byte data by 1. Write this byte to string. (max letter width formater)

FEEA - unused (messed addres)

FEEB - unused (messed addres)

FEEC - unused (messed addres)

FEED - unused (messed addres)

FEEE - unused (messed addres)

FEEF - unused (messed addres)

FEF0 - unused (messed addres)

FEF1 - unused (messed addres)

FEF2 - unused (messed addres)

FEF3 - unused (messed addres)

FEF4 - unused (messed addres)

FEF5 - unused (messed addres)

FEF6 - unused (messed addres)

FEF7 - unused (messed addres)

FEF8 - unused (messed addres)

FEF9 - unused (messed addres)

FEFA - unused (messed addres)

FEFB - unused (messed addres)

FEFC - unused (messed addres)

FEFD - unused (messed addres)

FEFE - unused (messed addres)

FEFF - remove FE from string by decrement byte data by 1. Set window state to 0xD. Write 0x0000 to 80114470 array. Write 0x0001 to 80114480 array. End string copying. Write FF to string.

FF - end string copying. Write FF to string. Set window state to 0x6. Write 0x0000 to 80114470 array.
