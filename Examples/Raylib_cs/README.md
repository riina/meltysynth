## Raylib-cs Example

This is an example implementation of a MIDI player backed by Raylib-cs.

Usage:
```cs
using System;
using Raylib_cs;
using MeltySynth;

class Program
{
    static int sampleRate = 44100;
    static int bufferSize = 4096;

    unsafe static void Main()
    {
        Raylib.InitWindow(800, 450, "MIDI Player");

        Raylib.InitAudioDevice();
        Raylib.SetAudioStreamBufferSizeDefault(bufferSize);

        var stream = Raylib.InitAudioStream((uint)sampleRate, 16, 2);
        var buffer = new short[2 * bufferSize];

        Raylib.PlayAudioStream(stream);

        var synthesizer = new Synthesizer("TimGM6mb.sf2", sampleRate);
        var sequencer = new MidiFileSequencer(synthesizer);
        var midiFile = new MidiFile(@"C:\Windows\Media\flourish.mid");

        sequencer.Play(midiFile, true);

        Raylib.SetTargetFPS(60);

        while (!Raylib.WindowShouldClose())
        {
            if (Raylib.IsAudioStreamProcessed(stream))
            {
                sequencer.RenderInterleavedInt16(buffer);
                fixed (short* p = buffer)
                {
                    Raylib.UpdateAudioStream(stream, (IntPtr)p, buffer.Length);
                }
            }

            Raylib.BeginDrawing();
            Raylib.ClearBackground(Color.LIGHTGRAY);
            Raylib.DrawText("MUSIC SHOULD BE PLAYING!", 255, 200, 20, Color.DARKGRAY);
            Raylib.EndDrawing();
        }

        Raylib.CloseWindow();
    }
}
```
