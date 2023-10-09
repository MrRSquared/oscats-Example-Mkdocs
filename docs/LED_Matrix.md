## LED Matrix
We can use an LED Matrix 

In order to run the game on the Raspberry Pi, I think our best entry may be This could work well with [PyGame](https://www.pygame.org/wiki/GettingStarted).

To display them on the Matrix panels, I basically found three tutorials that use the same library under the hood.

-We could try to use the LED Matrix as [Second Screen](https://learn.adafruit.com/raspberry-pi-led-matrix-display)


- There is also a project that writes [PyGame directly to the screen](https://github.com/backupify/pgmatrix)

- Finally, we could try to use the library directly to [draw to the matrix and refresh it manually](https://learn.adafruit.com/adafruit-rgb-matrix-bonnet-for-raspberry-pi) (This option will probably be the most work for us)
