# TS741

Replica del pedale overdrive [TS9 Ibanez](https://www.ibanez.com/eu/products/detail/ts9_99.html).

File simulazioni su LTspice ([download](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html)).

# Setup LTspice

Abbiamo usato un [modello dell'op-amp TL072](https://ltwiki.org/index.php?title=Components_Library_and_Circuits#Opamps) che non è presente nella libreria interna di LTspice, quindi bisogna importare manualmente i file `TL072.asy` e `TL072.sub` nella cartella dei componenti.

Per vedere qual è la cartella dei componenti di LTspice sul tuo computer:
- Aprire in LTspice una schermata di una schematic qualsiasi.
- Aprire la finestra dei componenti.
- Sotto `Top Directory:` c'è il percorso della cartella della libreria interna di LTspice (su Windows dovrebbe essere qualcosa del tipo `C:\users\<nome utente>\AppData\Local\LTspice\lib\sym`).
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

# Potenziometri
 
Un potenziometro con andamento lineare può essere costruito su SPICE come due resistori `Tone1` e `Tone2` rispettivamente di valori $m * R_{max}$ e $(1 - m) * R_{max}$ dove:
- $R_{max}$ è il valore massimo del potenziometro;
- $m$ è un parametro che va da $0$ a $1$ e indica quanto è aperto il potenziometro.

Per inserire questi valori nei resistori `Tone1` e `Tone2`, fare `Ctrl + click destro` sul resistore e inserire la formula tra parentesi graffe.
 
Con il comando `.param` si può fissare il valore di un parametro:
```
.param <nome_parametro> = <valore_parametro>
```

Un potenziometro con andamento logaritmico, invece, si rappresenta con due resistori di valore $R_{tot} * (1 - m^k)$ e $R_{tot} * m^k$.

Attenzione: l'elevamento a potenza lo si scrive come `m**k` e non `m^k`.
 
Con il comando `.step param` si può fare una simulazione delle grandezze del circuito ($V$ e $I$) al variare del parametro:
```
.step param <nome_parametro> <valore_min> <valore_max> <incremento>
```

## La resistenza $R_{min}$

Attenzione: per le simulazioni si vorrebbe far variare il parametro $m$ da $0$ (potenziometro tutto chiuso) a $1$ (potenziometro tutto aperto) ma LTspice non accetta valori di resistenza pari a $0$, quindi dà errore quando calcola i valori di $R$ per $m = 0$ e $m = 1$. Per ovviare a ciò si aggiunge ai valori di resistenza una $R_{min}$ molto bassa (`1m`, cioè $10^{-3}$).
