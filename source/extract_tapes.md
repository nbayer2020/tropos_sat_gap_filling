# Extract Data from the Tapes

### 1. First you will have to check if the tape machine is ready.

* Log into the LTO computer with:

```
ssh sat_data@lto5.tropos.de
(password: **AbS11!**)
```

* check the status with:

```
mtx -f /dev/sg1 status
```

* The data transfer element has to be
empty.
![Image 1](./images/extract_tapes_1.png)

If this isn't the case you have to unload it with:

```
mtx -f /dev/sg1 unload
```

### 2. Ask Hartwig for the key to access the server-room in building
23.5.

### 3. Use room **0.03** in the ground floor of 23.5 to access room
**0.07**. Here you will find the rack with the tape-computer and
tape-drive right next to the door.

### 4. At the tape-computer use the Next -> or Previous key <- to
navigate to "Operations". Use Enter to get to the selection of which of
the two magazines (each holding up to 4 tapes) you like to open.

![Image 2](./images/extract_tapes_2.png)

Either use Enter again on "Unlock Left Magazine" or "Unlock Right
Magazine" and wait a brief moment.

### 5. Kindly remove the unlocked magazine and insert or exchange
tape(s). Always start with #1 (nearest position in the left magazine).

![Image 3](./images/extract_tapes_3.png)

**Note** The left magazine contains:  *Positions 1* to *4* (starting with #1)

and the right magazine contains:  *Position 5* to *8* (starting with #5)

### 6. Re-insert the magazine and wait for the tape-computer to read the tape.

### 7. Turn off the light, leave the room and lock the door.

### 8. Give the key back to Hartwig.

### 9. Log into the LTO computer again

![Image 4](./images/extract_tapes_4.png)

### 10. Go to the directory for tapes with:

```
cd /vols/talos/datasets/eumcst/incoming/umarf/tapes
```

Execute the shell-script **extract_tape** for a single tape in *Positio 1*:

```
nohup extract_tape.sh $TAPENAME &
```

or the shell script **extract_tapes** for multiple tapes:

```
nohup extract_tapes.sh $TAPE1NAME $TAPE2NAME $TAPE3NAME &
```

For the tape names you can simply use ```mtx -f /dev/sg1 status``` again, it
shows them after VolumeTag=, remember to follow the position order.

**Note**: nohup lets you run programs even if you log out and writes any
messages into the file nohup.out.

### 11. Now the data for Step III should be under:

```
/vols/talos/datasets/eumcst/incoming/umarf/tapes/(tapename)
```

### 12. Unlike the http files the tape files don't need to be unpacked,
simply add them to the target folder:

```
/vols/altair/datasets/eumcst/incoming/umarf/http/(year)
```

**Note:** It can be useful to look into linux tape management to
understand the process and solve possibly occurring errors.

A starting point is:

[https://www.cyberciti.biz/hardware/unix-linux-basic-tape-management-commands/](https://www.cyberciti.biz/hardware/unix-linux-basic-tape-management-commands/)
