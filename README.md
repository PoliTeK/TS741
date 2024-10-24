# TS741

Replica del pedale overdrive [TS9 Ibanez](https://www.ibanez.com/eu/products/detail/ts9_99.html).

File simulazioni su LTspice ([download](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html)).

# Setup LTspice

Abbiamo usato un [modello dell'op-amp TL072](https://ltwiki.org/index.php?title=Components_Library_and_Circuits#Opamps) che non è presente nella libreria interna di LTspice, quindi bisogna importare manualmente i file `TL072.asy` e `TL072.sub` nella cartella dei componenti.

Per vedere qual è la cartella dei componenti di LTspice sul tuo computer:
- Aprire in LTspice una schermata di una schematic qualsiasi.
- Aprire la finestra dei componenti.
- Sotto `Top Directory:` c'è il percorso della cartella della libreria interna di LTspice (dovrebbe essere qualcosa del tipo `C:\users\<nome utente>\AppData\Local\LTspice\lib\sym`).
	- Se si sta eseguendo LTspice su Linux con Wine, la cartella `C:\users\<nome utente>\AppData\Local\LTspice\lib\sym` corrisponde alla cartella `~/.wine/drive_c/users/<nome utente>/AppData/Local/LTspice/lib/sym`.

Quindi spostare i file:
- `TL072.asy` nella cartella `C:\...\LTspice\lib\sym\OpAmps`.
- `TL072.sub` nella cartella `C:\...\LTspice\lib\sub`.

Dopo aver spostato i due file, riavviare LTspice per far comparire il modello del TL072 nella lista dei componenti e farlo comparire nella schematic della simulazione.

# Istruzioni per eseguire una simulazione con file audio esterno

- Nel repo trovate già un file audio `test.wav` con cui poter eseguire una simulazione.
- In alternativa, se volete eseguire una simulazione con un vostro file audio:
	- Prendete il file audio che avete e convertitelo in `.wav` (potete farlo su questo [convertitore online](https://online-audio-converter.com/it/)).
	- Rinominatelo `test.wav` e mettetelo nella stessa cartella del file della simulazione `TS741.asc`.
- Fate partire la simulazione e decidetene il tempo con il comando `.tran <tempo in secondi>`.
- Alla fine della simulazione troverete nella stessa cartella in cui è salvato il file della simulazione `TS741.asc`, anche un file di nome `output.wav` che contiene l'audio in ingresso distorto (potete convertirlo in `.mp3` sempre con lo stesso [convertitore online](https://online-audio-converter.com/it/)).

## Modifiche da fare per fare simulazione con file audio

- Per mettere il file audio in input bisogna mettere come `value` del generatore di ingresso `V2` il comando `WAVEFILE=<nome_file>.wav` (per esempio `WAVEFILE=test.wav`). Per variare il valore `value` bisogna fare `Ctrl + Click destro` sul generatore `V2`.
- Spice andrà a cercare quel file nella stessa cartella in cui è salvato il file della simulazione `TS741.asc`.
- Per avere un file in output si usa il comando `.wave <nome_file>.wav 16 44.1k V(<nodo_di_uscita>)` (se volete altre informazioni su che cosa fa questo comando nello specifico potete andare [qua](https://spiceman.net/ltspice-command-wave/#index_id1))

## Risoluzione errore `Bad wave file format found in test.wav`

https://www.reddit.com/r/diypedals/comments/oozekw/ltspice_wav_file_input/
