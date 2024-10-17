Platform: Win10 above X64

Default detect duration： 1000ms
Default shake offset: 10

How to run program:
1. Open 'cmd', locate into repo;
2. Run directly: CursorDemo.exe
3. Run customized paramters: CursorDemo.exe -interval:1000 -shake:100

### Source Code(C#)

```

using System.Drawing;
using System.Runtime.InteropServices; 

class Program
{
    [Flags]
    public enum MouseEventFlags : uint
    {
        LEFTDOWN = 0x00000002,
        LEFTUP = 0x00000004,
        MIDDLEDOWN = 0x00000020,
        MIDDLEUP = 0x00000040,
        MOVE = 0x00000001,
        ABSOLUTE = 0x00008000,
        RIGHTDOWN = 0x00000008,
        RIGHTUP = 0x00000010
    }

    [DllImport("user32.dll")]
    static extern bool GetCursorPos(out Point lpPoint);

    const String ARG_INTERVAL = "-interval:";
    const String ARG_SHAKE = "-shake:";

    [DllImport("user32.dll")]
    public static extern void mouse_event(MouseEventFlags flags, int dx, int dy, uint data, UIntPtr extraInfo);

    static void Main(String[] args)
    {
        int interval = 1000;
        int shake = 100;
        foreach (String arg in args)
        {
            if (arg.StartsWith(ARG_INTERVAL))
            {
                if (int.TryParse(arg.Replace(ARG_INTERVAL, String.Empty), out int result))
                {
                    interval = result;
                }
            }
            else if (arg.StartsWith(ARG_SHAKE))
            {
                if (int.TryParse(arg.Replace(ARG_SHAKE, String.Empty), out int result))
                {
                    shake = result;
                }
            }
        }
        bool positive = false;
        Point invalid = new(-100, -100);
        Point lastMousePosition = invalid;
        while (true)
        {
            GetCursorPos(out Point currentMousePosition);
            Console.WriteLine($"X: {currentMousePosition.X}, Y: {currentMousePosition.Y}");
            if (lastMousePosition != invalid && currentMousePosition.X == lastMousePosition.X && currentMousePosition.Y == lastMousePosition.Y)
            {
                if (currentMousePosition.X == 0 || currentMousePosition.Y == 0)
                {
                    positive = true;
                }
                int offset = positive ? shake : -shake;
                positive = !positive;
                mouse_event(MouseEventFlags.MOVE, offset, offset, 0, UIntPtr.Zero);
            }
            GetCursorPos(out lastMousePosition);
            Thread.Sleep(interval);
        }
    }
}

```
