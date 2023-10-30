# glasgow-tutorial

Tutorial how to implement glasgow applet

## Copy boilplate

```bash
mkdir -p software/glasgow/applet/interface/whatever
cp examples/boilerplate.py software/glasgow/applet/interface/whatever/__init__.py
```

## Rename class names

 * `BoilerplateSubtarget` - Elaboratable, class that returns amaranth module

 * `BoilerplateApplet` - Main applet code

     Also delete `, name="boilerplate"`

 * `BoilerplateAppletTestCase` - Tests

## Assign pins

Add names of pins you want to `_pins` in `GlasgowApplet` implementation. (For example `("rx", "tx")`)

You will have access to those pins in `Elaboratable` implementation constructor if you keep that part of `build` method in `GlasgowApplet` implementation.

To access `Signal` for one of pins, use `pads.rx_t` (if you want RX pin, for example, `_t` is appended by the glasgow lib)

## Implement amaranth module

You can now start implementing `Elaboratable` module. 

It is recommended to use constructor to store params for module.

Inside of `build` method, there is module already defined, now you just need to expand upon it.

Keep in mind module will "run" only as long as `interact` method of `GlasgowApplet` implementing did not return anything, so at this point you want to implement mechanism to like sleep or read line from stdin, to stop applet from exiting

## Communicating with applet, from FPGA

For communication with applet, you have in and out fifo.

Out fifo is for USB->FPGA direction and has:

 * `r_rdy` - There is data to read

 * `r_en` - Is FPGA ready to read next byte

 * `r_data` - One byte of data

In fifo is for USB<-FPGA communication and has:

 * `flush` - Send incomplete buffer

 * `w_data` - Data to be sent

 * `w_en` - There is data to be send

 * `w_rdy` - Buffer is ready to receive data

## Interacting with FPGA from applet

To talk to FPGA, you should use `iface` argument of `interact` method.

It has:

 * `read()` - async read buffer

 * `write(data)` - async write n bytes

 * `reset()` - reset fifo

 * `cancel()` - cancel async operations

 * `flush()` - write incomplete buffer

 * `statistics()` - print statistics for fifo
