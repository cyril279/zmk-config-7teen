## Troubleshooting poor encoder/keypress behavior
```
west build -p -b seeeduino_xiao -- -DSHIELD=7teen -DZMK_CONFIG='/home/cyril/workshop/zmk-config-7teen/config'
```

**Issue:**  
As designed, the encoder didn't work at all until moved to non-sercom pins, there were no keypresses until the encoder was actuated, and the encoder was some-timey after keypresses.  
No matter which gpio pins the encoder is assigned to, I still end up in a situation where the encoder needs to be actuated before any key-presses will register.  
Further, the fact that this issue is present without anything wired-up suggests that this is a configuration issue, not related to any hardware.  

**Breakthrough02**  
More testing exposed that only one encoder pin needed to be moved out of sercom02 range (other interrupt was still shared), which got me to thinking specifically about sercom2/i2c.  
Disabling I2C at board level as well as in the overlay worked. I am now back to the initial pinout, except for pin-c on the 3v3  
Still need to verify that there are actually two unique issues (retest with initial gpio_high |  pull_up)  

**Breakthrough01**  
working combo, 3 steps:  
1) move encoder ab pins into non-sercom range (further testing: let's look at the 5/7 interrupt issue again) 
2) flip encoder operation so that pins are (GPIO_ACTIVE_HIGH | GPIO_PULL_DOWN)  
3) encoder c pin is 3v3  
note: tio does not work with the encoder enabled.  

**As designed:**  
No keypresses until encoder actuation.
Encoder works amazingly until keypresses, then then it is some-timey.  

**Swap pins a8 & a9 - removing the encoder from the documented offending pin #5:**  
result: Encoder does not seem to respond at all, & no key-presses until encoder actuation.  
This defies exepctation.  

**Encoder on pins a10 & a11 (and a2 & a4)** -These pins are independent of any serial coms [(details)](https://github.com/Seeed-Studio/ArduinoCore-samd/blob/master/variants/XIAO_m0/variant.cpp#L22)  
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
