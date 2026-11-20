# aprx · fork EA7JRS

**aprx** es un iGate y digipeater APRS para Linux de Matti Aarnio (OH2MQK) y Kenneth Finnegan (W6KWF). Este fork lo mantiene compilable e instalable en **Debian 13 (Trixie)** y es el aprx que usa [S.A.T.-APRS](https://github.com/EA7JRS/sat-aprs).

El programa original y su documentación siguen en [`README`](README), [`INSTALL`](INSTALL) y `man 8 aprx`. Los cambios de este fork están en [`ChangeLog`](ChangeLog).

## Qué cambia respecto al original

| Cambio | Por qué |
| :--- | :--- |
| Compila con GCC 10 a 14 | `aprx.h`, `netresolver.c` y `aprx-stat.c`: variables globales declaradas `extern` (GCC 10+ usa `-fno-common`); `ttyreader.c`: prototipo ISO de `aprx_cfmakeraw()` (GCC 14 rechaza el `int` implícito). |
| `kiss.c` original | Una versión anterior del fork eliminaba los bytes no imprimibles, lo que estropea las tramas AX.25 binarias, y no enlazaba. |
| Paquete Debian moderno | `debhelper` 13, objetivos `build-arch` / `build-indep`. |
| Arranca al instalarlo | `STARTAPRX="yes"`, como el paquete de Debian al que sustituye, y el script de arranque lee `DAEMON_OPTS` de `/etc/default/aprx`. |

Versión actual: `2.9.1+ea7jrs2` (mayor que la `2.9.1-1` de Debian, así que la sustituye).

## Instalación en Debian 13

### Con S.A.T.-APRS

```bash
cd /opt/sat-aprs
sudo sh tools/instalar-aprx.sh
```

El script compila este fork y lo instala respetando su `/etc/aprx.conf`. Además lo retiene en apt para que no vuelva el de Debian, lo deja arrancado y avisa si falta el registro de radio (`rflog`).

### A mano

```bash
sudo apt install -y git build-essential dpkg-dev debhelper fakeroot
git clone https://github.com/EA7JRS/aprx.git && cd aprx
./configure && make make-deb
sudo dpkg -i ../aprx_2.9.1+ea7jrs2_*.deb
sudo apt-mark hold aprx          # que apt no lo cambie por el de Debian
sudo nano /etc/aprx.conf
sudo systemctl restart aprx
```

Para que S.A.T.-APRS (Radio → aprx) vea el tráfico, `aprx.conf` necesita el registro de radio dentro de `<logging>`:

```
<logging>
  rflog /var/log/aprx/aprx-rf.log
</logging>
```

## Licencia

BSD de tres cláusulas, como el aprx original: vea [`LICENSE`](LICENSE). Los cambios de este fork se publican bajo la misma licencia.

---

*English:* fork of aprx that builds and installs on Debian 13 (GCC 14, debhelper 13). Build with `./configure && make make-deb`, then `sudo dpkg -i ../aprx_*.deb`. See [`ChangeLog`](ChangeLog) for the changes.
