# netcat-1

import argparse
from http import client
import sys
import textwrap

def execute(cmd):
    cmd = cmd.strip()
    if not cmd:
        return
    output = subprocess.check_output(shlex.split(cmd),stderr=subprocess.STDOUT)
    return output.decode()

if __name__ == '__main__':
    parser = argparse.ArgumentParser(description='BHP Net Tool', formatter_class=argparse.RawDescriptionHelpFormatter, epilog=textwrap.dedent('''Example:
    netcat.py -t 192.168.1.100 -p 5555 -c  
        #comandnaya obolochka
    netcat.py -t 192.168.1.100 -p 5555 -u=mytest.txt
        # Loaded in file
    netcat.py -t 192.168.1.100 -p 5555 -e=\"cat /etc/password\"
        #move commande
    echo 'ABC' | ./netcat.py -t 192.168.1.100 -p 135
        # send text on port server 135
    netcat.py -t 192.168.1.100 -p 5555'''))
        # connect with server
parser.add_argument('-c', '--command', action='store_true',help='command shell')
parser.add_argument('-e', '--execute', help='execute sprcified command')
parser.add_argument('-l', '--listen', action='store_true', help='listen')
parser.add_argument('-p', '--port', type=int, default=5555, help='specified IP')
parser.add_argument('-t', '--target', default='192.168.1.203', help='specified IP')
parser.add_argument('-u', '--upload', help='upload file')
args = parser.parse_args()
if args.listen:
    buffer = ''
else:
    buffer = sys.stdin.read()

nc = NetCat(args, buffer.encode())
nc.run()

# continue for NetCat - proslushivanie

class Netcat:
    def __init__(self, args, buffer=None): # innizialization object NetCat
        self.args = args
        Self.buffer = buffer
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM) # creat object socket
        self.socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEDDR, 1)

    def run(self): #run - deligiring two metods (listen or send)
        if self.args.listen: 
            self.listen()
        else:
            self.send()

# continue

def send(self):
    self.socket.connect((self.args.target, self.args.port)) # connect to server
    if self.buffer:
        self.socket.send(self.buffer)

try: # use block
    while True: # begin cycle
        recv_len = 1
        response = ''
        while recv_len:
            data = self.socket.recv(4096)
            recv_len = len(data)
            response += data.decode()
            if recv_len < 4096:
                break # exit to loop
        if response:
            print(response)
            buffer = input('> ')
            buffer += '\n'
            self.socket.send(buffer.encode()) # if no! output to answer. stop. 

except KeyboardInterrupt: # continue cycle !!!
    print('User terminated.')
    self.socket.close()
    sys.exit()
    
# !!! While budet rabotat`, poka ego ne ostanovit` (Ctrl+C)

# Continue for stdin
def listen(self):
    self.socket.bind((self.args.target, self.args.accept)) # def listen - privyazivaetsya k adress i port
    self.socket.listen(5)
    while True: # and begin proslushivat` in while
        client_socket, _ = self.socket.accept()
        client_thread = threading.Thread(target=self.handle, args=(client_socket,)) # send socket connect to (k) method handle
        client_thread.start() #32 page

# continue : Logic for load files & command execution interactiv shell
def handle(self, client_socket):
    if self.args.execute: # 
        output = execute(self.args.execute)
        client_socket.send(output.encode())

    elif self.args.upload: #
        file_buffer = b''
        while True:
            data = client_socket.recv(4096)
            if data:
                file_buffer += data
            else:
                break

    with open(self.args.upload, 'wd') as f:
        f.write(file_buffer)
    message = f'Saved file {self.args.upload}'
    client_socket.send(message.encode())

    elif self.args.command: #
    cmd_buffer = b''
    while True:
        try:
            client_socket.send(b'BHP: #> ')
            while '\n' not in cmd_buffer.decode():
                cmd_buffer += client_socket.recv(64)
            response = execute(cmd_buffer.decode())
            if response:
                client_socket.send(response.encode())
            cmd_buffer = b''
        except Exception as e:
            print(f'server killed {e}')
            self.socket.close()
            sys.exit()
