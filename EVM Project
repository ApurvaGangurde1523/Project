; Electronic Voting Machine 
; 8051 Microcontroller Assembly Code

ORG 0000H
SJMP MAIN

ORG 0030H

; Define Data Section
VOTE_C1:           DB 00H       ; Votes for Candidate 1
VOTE_C2:           DB 00H       ; Votes for Candidate 2
VOTE_C3:           DB 00H       ; Votes for Candidate 3
VOTE_TOTAL:        DB 00H       ; Total votes cast
RESULT_CR1:        DB 00H       ; Candidate with max votes
RESULT_CR2:        DB 00H       ; Candidate with second max votes

MAIN:
    ; Initialize LCD
    CALL INIT_LCD

    ; Initialize vote counters
    MOV VOTE_C1, #00H        ; Counter for Candidate 1
    MOV VOTE_C2, #00H        ; Counter for Candidate 2
    MOV VOTE_C3, #00H        ; Counter for Candidate 3
    MOV VOTE_TOTAL, #00H     ; Total votes count

VOTING_LOOP:
    ; Display voting options
    CALL DISPLAY_OPTIONS

    ; Wait for keypad input
    CALL GET_KEYPAD_INPUT
    
    ; Determine which candidate was selected
    CJNE A, #01H, CHECK_CANDIDATE2
    INC VOTE_C1               ; Increment Candidate 1's vote count
    SJMP CAST_VOTE

CHECK_CANDIDATE2:
    CJNE A, #02H, CHECK_CANDIDATE3
    INC VOTE_C2               ; Increment Candidate 2's vote count
    SJMP CAST_VOTE

CHECK_CANDIDATE3:
    CJNE A, #03H, VOTING_LOOP   ; If not a valid choice, restart voting
    INC VOTE_C3               ; Increment Candidate 3's vote count

CAST_VOTE:
    ; Increment total votes
    INC VOTE_TOTAL
    CALL LED_INDICATE         ; Indicate vote has been cast

    ; Check if total votes have reached 25
    MOV A, VOTE_TOTAL
    CJNE A, #25, VOTING_LOOP  ; If not 25, continue voting

    ; Determine the results
    CALL DETERMINE_RESULT
    CALL DISPLAY_RESULT
    SJMP $                     ; Infinite loop to stop further processing

; Function to light up LED for indication
LED_INDICATE:
    SETB P0.0                 ; Turn on LED1
    ACALL DELAY
    CLR P0.0                  ; Turn off LED1
    RET

; Keypad input function
GET_KEYPAD_INPUT:
    ; Simulate reading from keypad; replace with actual keypad logic
    ; Assuming a simple mapping for the keypad:
    ; 1 - Candidate 1
    ; 2 - Candidate 2
    ; 3 - Candidate 3
    ; Returns value in A
    MOV P1, #0FFH             ; Set Port 1 as input
WAIT_FOR_PRESS:
    MOV A, P1
    ANL A, #0F0H              ; Mask lower nibble
    JNZ WAIT_FOR_PRESS        ; Wait until a key is pressed
    MOV A, P1                 ; Read pressed key
    ANL A, #0F0H              ; Mask to get row
    SWAP A                    ; Move row info to high nibble
    ; Determine which key was pressed
    CJNE A, #0x10, CHECK_CANDIDATE2_KEY
    MOV A, #01H               ; Set A to 1 (Candidate 1)
    RET

CHECK_CANDIDATE2_KEY:
    CJNE A, #0x20, CHECK_CANDIDATE3_KEY ; Check if pressed key is '2'
    MOV A, #02H               ; Set A to 2 (Candidate 2)
    RET

CHECK_CANDIDATE3_KEY:
    CJNE A, #0x30, VOTING_LOOP ; If not a valid choice, restart voting
    MOV A, #03H               ; Set A to 3 (Candidate 3)
    RET

; Function to display voting options on the LCD
DISPLAY_OPTIONS:
    ; Clear the LCD first
    CALL CLEAR_LCD
    ; Display options for voting
    MOV DPTR, #OPTION_MESSAGE  ; Load address of message
    CALL LCD_WRITE_STRING       ; Display the options
    RET

OPTION_MESSAGE:
    DB 'Vote: 1 for CR1', 0
    DB '2 for CR2', 0
    DB '3 for CR3', 0

; Determine the results after voting
DETERMINE_RESULT:
    ; Compare votes and store results
    MOV A, VOTE_C1
    MOV B, VOTE_C2
    CJNE A, B, CANDIDATE1_IS_MAX
    MOV RESULT_CR1, #01H ; Candidate 1 and 2 are tied
    MOV A, VOTE_C3
    CJNE A, B, CANDIDATE2_IS_MAX
    MOV RESULT_CR2, #03H ; Candidate 3 wins
    SJMP STORE_RESULTS

CANDIDATE1_IS_MAX:
    MOV A, VOTE_C3
    CJNE A, VOTE_C2, CANDIDATE1_IS_WINNER
    MOV RESULT_CR1, #01H ; Candidate 1 wins
    MOV RESULT_CR2, #02H ; Candidate 2 is second
    SJMP STORE_RESULTS

CANDIDATE1_IS_WINNER:
    MOV RESULT_CR1, #01H ; Candidate 1 wins
    MOV RESULT_CR2, #03H ; Candidate 3 is second
    SJMP STORE_RESULTS

CANDIDATE2_IS_MAX:
    MOV A, VOTE_C3
    CJNE A, VOTE_C1, CANDIDATE2_IS_WINNER
    MOV RESULT_CR1, #02H ; Candidate 2 wins
    MOV RESULT_CR2, #01H ; Candidate 1 is second
    SJMP STORE_RESULTS

CANDIDATE2_IS_WINNER:
    MOV RESULT_CR1, #02H ; Candidate 2 wins
    MOV RESULT_CR2, #03H ; Candidate 3 is second
    SJMP STORE_RESULTS

STORE_RESULTS:
    ; Placeholder for storing the result
    RET

; Display results on the LCD
DISPLAY_RESULT:
    ; Clear LCD
    CALL CLEAR_LCD
    ; Display results for CR1 and CR2
    MOV A, RESULT_CR1
    CALL LCD_WRITE_RESULT
    MOV A, RESULT_CR2
    CALL LCD_WRITE_RESULT
    RET

LCD_WRITE_RESULT:
    ; Display the candidate result
    MOV DPTR, #RESULT_MESSAGE
    MOVC A, @A+DPTR         ; Load the message to display
    CALL LCD_WRITE
    RET

RESULT_MESSAGE:
    DB 'CR1: ', 0, 'CR2: ', 0

; Delay function for LED indication
DELAY:
    MOV R1, #255
    MOV R2, #255
DELAY_LOOP:
    DJNZ R2, DELAY_LOOP
    DJNZ R1, DELAY_LOOP
    RET

; LCD Initialization
INIT_LCD:
    ; LCD initialization routine
    MOV A, #38H              ; Function set
    CALL LCD_CMD
    MOV A, #0EH              ; Display ON
    CALL LCD_CMD
    MOV A, #01H              ; Clear display
    CALL LCD_CMD
    MOV A, #06H              ; Entry mode
    CALL LCD_CMD
    RET

; Clear LCD
CLEAR_LCD:
    MOV A, #01H              ; Command to clear LCD
    CALL LCD_CMD
    RET

; LCD Command subroutine
LCD_CMD:
    MOV P2, A                ; Send command to data port
    CLR P3.0                 ; RS = 0 for command
    CLR P3.1                 ; RW = 0 for write
    SETB P3.2                ; Enable high
    CLR P3.2                 ; Enable low
    RET

; LCD Write Function
LCD_WRITE:
    MOV P2, A                ; Send data to data port
    SETB P3.0                ; RS = 1 for data
    CLR P3.1                 ; RW = 0 for write
    SETB P3.2                ; Enable high
    CLR P3.2                 ; Enable low
    RET

; LCD Write String Function
LCD_WRITE_STRING:
    MOV A, @DPTR
    JZ DONE                       ; If null terminator, end
    CALL LCD_WRITE     ; Write character to LCD
    INC DPTR                     ; Move to next character
    SJMP LCD_WRITE_STRING
DONE:
    RET

END
