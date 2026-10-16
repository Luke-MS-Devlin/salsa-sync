# Music

The app's code is MIT licensed (see `../LICENSE`). The recordings in this folder are **not** covered by the MIT licence. Each one keeps the Creative Commons licence its artist chose, listed below. All of them are non-commercial.

| File | Song | Artist | Album | Licence | Changes |
|---|---|---|---|---|---|
| `cupido.mp3` | Cupido | Frank Guerrero y Su Grupo Aché | En Beta 0.1 | [CC BY-NC-ND 3.0](https://creativecommons.org/licenses/by-nc-nd/3.0/) | None. This is the original file, played at its own tempo (about 170 BPM). |
| `vamos.mp3` | Vamos A Tocar Sonero | Frank Guerrero y Su Grupo Aché | En Beta 0.1 | [CC BY-NC-ND 3.0](https://creativecommons.org/licenses/by-nc-nd/3.0/) | None. This is the original file, played at its own tempo (about 190 BPM). |
| `mujer-140.mp3` … `mujer-200.mp3` | La Mujer de Antonio | SONGO 21 | Studio sessions 2003 | [CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/) | An excerpt (96 s to 204 s), changed to 140, 160, 180 and 200 BPM with the pitch kept the same (ffmpeg `atempo`), mono. These adapted files are shared under the same CC BY-NC-SA 3.0 licence. |

All three songs come from the [Free Music Archive](https://freemusicarchive.org/).

## songs.json

`songs.json` lists, for each file, the time in seconds of every beat. Each list starts on a "5", so that the app's 5-6-7-8 count-in lines up with the song.

The beat times were worked out like this:

1. **Spectral flux.** This measures how much the sound's frequency content jumps up at each moment. The jumps are where drums and other instruments hit.
2. **Beat tracking.** A dynamic-programming beat tracker (Ellis, 2007) picks beats that land on strong hits while keeping a steady gap between them.
3. **Fine-tuning.** Each beat is moved to the strongest hit within 40 ms, and a short running line fit then smooths out jitter.
4. **Accuracy check.** On audio with known beats, the result was within 5 ms.
5. **Checked by ear.** The beat lists were listened to with clicks laid over the music.

A live band speeds up and slows down slightly, by up to about 50 ms over a few bars, so the app uses these real beat times instead of a fixed tempo.
