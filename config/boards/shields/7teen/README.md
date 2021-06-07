## Troubleshooting poor encoder/keypress behavior
```
west build -p -b seeeduino_xiao -- -DSHIELD=7teen -DZMK_CONFIG='/home/cyril/workshop/zmk-config-7teen/config'

cmake --build /home/cyril/workbench/github/zmk/app/build --target clean
# or first:
rm build/CMakeCache.txt
```

**Issue(s):**  
As initially designed, there were several issues:  
1) The encoder didn't work at all until ab contacts were moved to non-sercom pins of the controller  
2) There were no keypresses until the encoder was actuated  
3) The encoder was some-timey after keypresses.  
4) There was ~~no~~ unpredictable output from RowD (pin 7) if the encoder was enabled (or included in the array)  
No matter which gpio pins the encoder is assigned to, I still end up in a situation where the encoder needs to be actuated before any key-presses will register.  
Further, the fact that this issue is present without anything wired-up suggests that this is a configuration issue, not related to any hardware beyond the controller.  

**Solution (as of 2021/05/28):** [(details in commit)](https://github.com/cyril279/zmk-config-7teen/commit/bbbfaca4e6dfe6af894ac9cbdde0b3732fcae70d)  
Primarily relevant b/c shield was fabricated prior to proper amounts of testing.  
- Reverse polarity of encoder circuit: Resolves the need to actuate the encoder prior to seeing keypresses.  
- Disable I2C: Resolves the wonky encoder behavior & the need to use non-serial-com gpio's.  
- Enable KSCAN_MATRIX_POLLING: Alleviates shared interrupt conflict across pins 5 & 7.  

**Lesssons learned / design notes:**  
- Breadboard first : /  
- Regarding xiao, watch the usage of interrupt-shared pins:  
  - Ideally, keep encoders out of the sercom (I2C/UART/SPI) range [(pin details)](https://github.com/Seeed-Studio/ArduinoCore-samd/blob/master/variants/XIAO_m0/variant.cpp#L22)  
  - Key-matrix spread across sercom(s) seems fine  
