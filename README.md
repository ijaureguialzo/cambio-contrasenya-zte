# Cambio de contraseña ZTE H3640

Queremos modificar la contraseña del usuario `admin` del router ZTE ZXHN H3640 V10 pero no podemos cambiarla porque el
interfaz web no lo permite.

## Prerrequisitos

- Conocer la contraseña del usuario `admin`.
- Estar conectado en un interfaz por cable, no funciona conectado a la wifi.
- Python 3.10.

## Pasos a seguir

1. Descargar [zte_modem_tools](https://github.com/douniwan5788/zte_modem_tools).

2. Instalar las dependencias:

    ```shell
    python3 -m venv .venv
    source ./.venv/bin/activate
    pip install -r requirements.txt
    ```

3. Obtener el password de acceso por telnet:

    ```
    python3 zte_factroymode.py --user admin --pass ***** --ip 10.1.0.1 --port 80 telnet open
    ```

   Si el comando tiene éxito muestra algo como:

    ```
    b'FactoryModeAuth.gch?user=*****&pass=*****&port=62323'
    ```

4. Conectar por telnet con el usuario, contraseña y puerto obtenidos en el paso anterior:

    ```
    telnet 10.1.0.1 62323
    ```

5. Mostrar la lista de usuarios:

    ```
    sendcmd 1 DB p DevAuthInfo
    ```

    ```xml
    <Tbl name="DevAuthInfo" RowCount="7">
            <Row No="0">
                    <DM name="ViewName" val="IGD.AU1"/>
                    <DM name="Enable" val="1"/>
                    <DM name="IsOnline" val="0"/>
                    <DM name="AppID" val="1"/>
                    <DM name="User" val="***"/>
                    <DM name="Pass" val="***"/>
                    <DM name="Level" val="1"/>
                    <DM name="ChgPwd" val="0"/>
                    <DM name="AccessIP" val="10.1.140.207"/>
                    <DM name="LoginTime" val=""/>
                    <DM name="Extra" val=""/>
                    <DM name="ExtraInt" val="0"/>
            </Row>
            <Row No="1">
                    <DM name="ViewName" val="IGD.AU2"/>
                    <DM name="Enable" val="1"/>
                    <DM name="IsOnline" val="0"/>
                    <DM name="AppID" val="1"/>
                    <DM name="User" val="***"/>
                    <DM name="Pass" val="***"/>
                    <DM name="Level" val="2"/>
                    <DM name="ChgPwd" val="0"/>
                    <DM name="AccessIP" val="10.1.140.207"/>
                    <DM name="LoginTime" val=""/>
                    <DM name="Extra" val=""/>
                    <DM name="ExtraInt" val="0"/>
            </Row>
            <Row No="2">
                    <DM name="ViewName" val="IGD.AU3"/>
                    <DM name="Enable" val="1"/>
                    <DM name="IsOnline" val="0"/>
                    <DM name="AppID" val="2"/>
                    <DM name="User" val="***"/>
                    <DM name="Pass" val="***"/>
                    <DM name="Level" val="2"/>
                    <DM name="ChgPwd" val="1"/>
                    <DM name="AccessIP" val=""/>
                    <DM name="LoginTime" val=""/>
                    <DM name="Extra" val=""/>
                    <DM name="ExtraInt" val="0"/>
            </Row>

            <!-- Resto de la salida -->
    </Tbl>
    ```

   Los dos primeros tienen IP de acceso reciente:

    ```
    Row 0 -> admin
    Row 1 -> 1234
    ```

6. Forzar un cambio de contraseña del usuario admin:

    ```
    sendcmd 1 DB set DevAuthInfo 0 ChgPwd 1
    sendcmd 1 DB save
    ```

   Después acceder al [interfaz web](http://10.1.0.1) con las credenciales y pedirá cambiar la contraseña.

7. Desactivar el usuario 1234:

    ```
    sendcmd 1 DB set DevAuthInfo 1 Enable 0
    sendcmd 1 DB save
    ```

## Referencias

- [Acceso como administrador con el usuario 1234 en el Router Zte H3640 V10 de MásMóvil, consiguiendo datos SIP](https://bandaancha.eu/foros/cambiar-router-zte-h3640-masmovil-uno-1752941)
