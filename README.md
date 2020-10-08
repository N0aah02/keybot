# control
# ========== IMPORTS ==========

import pynput as pp # pip install pynput
import tkinter as tk
import webbrowser as web
from pynput.keyboard import Key, Controller
import threading as tr
import sys
from time import sleep
from tkinter import messagebox as msgBox

# ========== VARIABLES ==========

rec_bind_text = 'q'
rec_bind = 'q'
mode = 'idle'
keylogger = []
keylogger_correcttype = []

# GUI creation
win = tk.Tk()
fr = tk.Frame(win, bg='blue')
fr.pack(side='top')

# ========== COMMANDS ==========

print()
def rec_key():
    mode = 'rec'
    print('Recording...')
    def on_press(key):
        keylogger_correcttype.append(key)
        clear = str(key)
        clear = clear.replace("'","")
        clear = clear.replace("'","")

        if mode == 'rec':
            if clear == rec_bind:
                del keylogger_correcttype[-1]

                if startBtn['state'] == 'normal':
                    play()

                else:
                    startBtn['state'] = 'normal'
                    tip['text'] = f'Press the circle or <{rec_bind_text}> to play'
                    listenerstatus = False
                    print(f'Listener Status: {listenerstatus}')
                    return False
            else:
                keylogger.append(clear)

                klg = str(keylogger)
                klg = klg.replace("'", '')
                klg = klg.replace(' ', '')
                klg = klg.replace(',',' ')
                print(klg)
                print(mode)

    def on_release(key):
        pass

    # Collect events until released
    with pp.keyboard.Listener(
            on_press=on_press,
            on_release=on_release) as listener:
        listener.join()

    # ...or, in a non-blocking fashion:
    listener = pp.keyboard.Listener(
        on_press=on_press,
        on_release=on_release)
    listener.start()

maintr = tr.Thread(target=rec_key)
maintr.daemon = True

def rec():
    keylogger.clear()
    print()
    tip['text'] = f'Press <{rec_bind_text}> to stop recording!'
    startBtn['state'] = 'disabled'
    try:
        maintr.start()
    except:
        print('Main thread could not be started. Have to assume it\'s already running.') # inspiration for this error message: Minecraft's debug log
virtual_keyboard = Controller()

def play():
    mode = 'play'
    print(mode)
    if keylogger_correcttype == []:
        msgBox.showerror(title='Error', message='Please record first!')

    else:
        for k in range(len(keylogger_correcttype)):
            tip['text'] = f'Pressing {keylogger_correcttype[int(k)]}'
            virtual_keyboard.press(keylogger_correcttype[int(k)])
            sleep(0.05)
            virtual_keyboard.release(keylogger_correcttype[int(k)])
        tip['text'] = 'Press the circle to start recording again!'
def log():
    print(f'''{keylogger}''')

def infoopen():
    web.open('bit.ly/styx-kbrc')

def support():
    web.open('https://github.com/nsde/stuff/wiki/Keyboard-Recorder-Help')

def mail():
    web.open('mailto:neostyx@pm.me')

def about():
    web.open('https://sites.google.com/view/styx-navigator')

def nothing():
    pass

def exitapp():
    pp.keyboard.Listener.stop
    win.destroy()
    sys.exit(0)

# ========== GUI ==========

win.title('Keyboard Recorder (http://bit.ly/styx-kbrc)')
win.config(bg='blue')

info = tk.Button(fr, text='Keyboard Recorder', fg='white', font=('Arial', 20 , 'bold', 'underline'),
bg='blue', relief='flat', command=infoopen)
info.pack()

tip = tk.Button(fr, text='Press the circle to start recording!', fg='white', font=('Arial', 10 , 'bold', 'italic'),
bg='blue', relief='flat', command=nothing)
tip.pack()

startBtn = tk.Button(fr, text='⬤', command=rec, fg='white', font=('Arial', 35), bg='darkblue', relief='flat')
startBtn.pack(side='left')

startplayBtn = tk.Button(fr, text='▶', command=play, fg='white', font=('Arial', 35), bg='darkblue', relief='flat')
startplayBtn.pack(side='left')

LogBtn = tk.Button(fr, text='Log in Shell', command=log, fg='white', font=('Arial', 10), bg='blue', relief='flat')
LogBtn.pack()

supportBtn = tk.Button(fr, text='Help', command=support, fg='white', font=('Arial', 10), bg='blue', relief='flat')
supportBtn.pack()

mailBtn = tk.Button(fr, text='Mail us', command=mail, fg='white', font=('Arial', 10), bg='blue', relief='flat')
mailBtn.pack()

aboutBtn = tk.Button(fr, text='About NeoStyx', command=about, fg='white', font=('Arial', 10), bg='blue', relief='flat')
aboutBtn.pack()

exitBtn = tk.Button(fr, text='Exit', command=exitapp, fg='yellow', font=('Arial', 10, 'bold'), bg='blue', relief='flat')
exitBtn.pack()

win.mainloop()
