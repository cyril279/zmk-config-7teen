## Troubleshooting poor encoder/keypress behavior
```
west build -p -b seeeduino_xiao -- -DSHIELD=7teen -DZMK_CONFIG='/home/cyril/workshop/zmk-config-7teen/config'
```

**Issue:**  
There is an interrupt issue, where the #5 & #7 pins should all be in the matrix, because these specific pins share interrupt#9.  

**As designed:**  
No keypresses until encoder actuation.
Encoder works amazingly until keypresses, then then it is some-timey.  

**Swap pins a8 & a9 - removing the encoder from the documented offending pin #5:**  
result: Encoder does not seem to respond at all, & no key-presses until encoder actuation.  
This defies exepctation. I am not sure exactly what the real conflict is.

**Encoder on pins a8 & a11**  
untested

**Encoder as part of the key-matrix**  
untested

**Force matrix scan:**  
add lines to Kconfig.defconfig to force polling the matrix for key-presses  
```
config ZMK_KSCAN_MATRIX_POLLING
    default y
```
result: Encoder works wonderfully, keypresses don't work UNTIL the encoder has been actuated.

**Force direct-pin scan:**  
add lines to Kconfig.defconfig to force polling for direct pins  
```
config ZMK_KSCAN_DIRECT_POLLING
    default y
```
result: Same as 'As designed', so no effect.

**Force matrix scan AND direct pins?**  
```
config ZMK_KSCAN_DIRECT_POLLING
    default y

config ZMK_KSCAN_MATRIX_POLLING
    default y
```
result: same as matrix_polling alone.  
It seems that the direct_polling has no effect on this system at all.
