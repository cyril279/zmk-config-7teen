## Troubleshooting poor encoder/keypress behavior
```
west build -p -b seeeduino_xiao -- -DSHIELD=7teen -DZMK_CONFIG='/home/cyril/workshop/zmk-config-7teen/config'
```

**Issue:**  
~~There is an interrupt issue, where the #5 & #7 pins should not be assigned to separate elements because these specific pins share interrupt#9.~~  
This does not seem to be the shared-interrupt issue. No matter which gpio pins I assign the encoder pins, I still end up in a situation where the encoder needs to be actuated before any key-presses will register (unless I force-poll the matrix).

**As designed:**  
No keypresses until encoder actuation.
Encoder works amazingly until keypresses, then then it is some-timey.  

**Swap pins a8 & a9 - removing the encoder from the documented offending pin #5:**  
result: Encoder does not seem to respond at all, & no key-presses until encoder actuation.  
This defies exepctation.  

**Encoder on pins a10 & a11 (and a2 & a4)** -these are all analog pins  
result: No keypresses until encoder actuation.
Encoder works well before AND after key-presses.

**Force matrix scan:**  
add lines to Kconfig.defconfig to force polling the matrix for key-presses  
```
config ZMK_KSCAN_MATRIX_POLLING
    default y
```
result: No keypresses until encoder actuation.
Encoder works well before AND after key-presses.
