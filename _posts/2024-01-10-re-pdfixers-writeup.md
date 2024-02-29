---
layout: post
title: "[RE] Reversing PDFixers.exe writeup"
categories: hacking writeup re js dotnet
---

## Introduction
I came across this file named PDFixers.exe because of some alerts from the IPS system tripping off since a user downloaded and ran this file. Upon doing a VirusTotal check, the file appeared safe: non of the antivirus vendors marked it possitive, even the almighty Bkav Pro. Out  of curiosity, I decided to take a look at it. And it surely was a fun journey.

![virustotal](/assets/images/pdfixers/virustotal.png)
![bkav](/assets/images/pdfixers/bkav.png)

## From .NET binary
After a quick check, the file turned out to be a .NET binary file. So I used [ILSpy](https://github.com/icsharpcode/ILSpy) to take a look at its source code. Although I like ILSpy as a tool, I prefer using my editor to browse the code. So I exported the disassembled code into a folder.

![structure](/assets/images/pdfixers/code_structure.png)

Right off the bat, the source code was really small, the big thing that made up the 8.3MB binary was a resource file that contained a zip file of SumatraPDF (my favorite PDF reader btw). After checking the SumatraPDF.exe file, the file was safe and its hash matches the one downloaded from the official website.

![code](/assets/images/pdfixers/code.png)

I also saw some suspicious functions here. The thing is, these functions had no references in the whole source code. So the binary must be safe right? Not quite!

```cs
[DesignerGenerated]
public class Form1 : Form
{
	private IContainer components;

	[field: AccessedThroughProperty("WebBrowser1")]
	internal virtual WebBrowser WebBrowser1
	{
		get; [MethodImpl(MethodImplOptions.Synchronized)]
		set;
	}

	public Form1()
	{
		InitializeComponent();
		WebBrowser1.ObjectForScripting = new Install();
		WebBrowser1.DocumentText = Resources.installerHTML;
	}

	[DebuggerNonUserCode]
	protected override void Dispose(bool disposing)
	{
		try
		{
			if (disposing && components != null)
			{
				components.Dispose();
			}
		}
		finally
		{
			base.Dispose(disposing);
		}
	}

	[System.Diagnostics.DebuggerStepThrough]
	private void InitializeComponent()
	{
		System.ComponentModel.ComponentResourceManager resources = new System.ComponentModel.ComponentResourceManager(typeof(Pdfixers.Form1));
		this.WebBrowser1 = new System.Windows.Forms.WebBrowser();
		base.SuspendLayout();
		this.WebBrowser1.IsWebBrowserContextMenuEnabled = false;
		this.WebBrowser1.Location = new System.Drawing.Point(1, 2);
		this.WebBrowser1.MinimumSize = new System.Drawing.Size(20, 20);
		this.WebBrowser1.Name = "WebBrowser1";
		this.WebBrowser1.ScriptErrorsSuppressed = true;
		this.WebBrowser1.ScrollBarsEnabled = false;
		this.WebBrowser1.Size = new System.Drawing.Size(777, 632);
		this.WebBrowser1.TabIndex = 1;
		base.AutoScaleDimensions = new System.Drawing.SizeF(6f, 13f);
		base.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
		base.ClientSize = new System.Drawing.Size(778, 629);
		base.Controls.Add(this.WebBrowser1);
		base.Icon = (System.Drawing.Icon)resources.GetObject("$this.Icon");
		base.Margin = new System.Windows.Forms.Padding(1);
		base.Name = "Form1";
		base.FormBorderStyle = System.Windows.Forms.FormBorderStyle.None;
		base.StartPosition = System.Windows.Forms.FormStartPosition.CenterScreen;
		this.Text = "Pdfixers Installer";
		base.ResumeLayout(false);
	}
}
```
Here we can see a `WebBrowser` object is initialized, which then takes control of an instance of `Install`, and the browser shows the content inside `Resources.installerHTML`.

![html](/assets/images/pdfixers/html.png)

Let's take a look at it then.

## To obfuscated JS

As I have expected, the JS code was obfuscated, but I didn't give up. First, I formatted the JS, then replaced all the hex numbers with decimal numbers for readability.
200 lines of obfuscated JS code was quite scary, but I noticed something: there were lots of `fn(number)` patterns. Almost every function had it, except for these two:

```js
  function a0y(o, D) {
    var U = a0b();
    return a0y = function (X, V) {
      X = X - 234;
      var i = U[X];
      if (a0y.nmictG === undefined) {
        var b = function (R) {
          var G = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+/=";
          var S = "", c = "", x = S + b;
          for (var w = 0, P, Q, a = 0; Q = R.charAt(a++); ~Q && (P = w % 4 ? P * 64 + Q : Q, w++ % 4) ? S += x.charCodeAt(a + 10) - 10 !== 0 ? String.fromCharCode(255 & P >> (-2 * w & 6)) : w : 0) {
            Q = G.indexOf(Q);
          }
          for (var p = 0, t = S.length; p < t; p++) {
            c += "%" + ("00" + S.charCodeAt(p).toString(16)).slice(-2);
          }
          return decodeURIComponent(c);
        };
        a0y.hhmlHI = b, o = arguments, a0y.nmictG = true;
      }
      var y = U[0], E = X + y, g = o[E];
      if (!g) {
        var R = function (G) {
          this.RZuHHN = G, this.OWVSGM = [1, 0, 0], this.QPluiE = function () {
            return "newState";
          }, this.drAqWp = "\\w+ *\\(\\) *{\\w+ *", this.xsoCDC = "['|\"].+['|\"];? *}";
        };
        R.prototype.Xdbwdm = function () {
          var G = new RegExp(this.drAqWp + this.xsoCDC), S = G.test(this.QPluiE.toString()) ? --this.OWVSGM[1] : --this.OWVSGM[0];
          return this.QWJoTM(S);
        }, R.prototype.QWJoTM = function (G) {
          if (!Boolean(~G)) return G;
          return this.hCONag(this.RZuHHN);
        }, R.prototype.hCONag = function (G) {
          for (var S = 0, c = this.OWVSGM.length; S < c; S++) {
            this.OWVSGM.push(Math.round(Math.random())), c = this.OWVSGM.length;
          }
          return G(this.OWVSGM[0]);
        }, new R(a0y).Xdbwdm(), i = a0y.hhmlHI(i), o[E] = i;
      } else i = g;
      return i;
    }, a0y(o, D);
  }
  function a0b() {
    var F = ["ndu1mJG3yxDRtu5K", "zgvIDq", "x19WCM8", "BMn0Aw8", "Dg9FxW", "DgLVBG", "zxjYB3i", "zxHJzxa", "EgvSlNa", "C2vHCMm", "Au1tBgi", "zsKGE30", "uwXKq0G", "q0zbBLC", "y2HHAw4", "yxbWBhK", "DgfIBgu", "zNvUy3q", "BvP3Cei", "BIaOzNu", "ys16qs0", "E30Uy28", "DK5LBhi", "DhLWzq", "jf0Qkq", "yMLUza", "mZm0mZKYmfzbtLzTtG", "D1z6uhq", "rvnfv20", "qvLfELC", "C3rHDgu", "Dg9tDhi", "CNvJDg8", "y29UC28", "rwv2wu8", "EKeTwL8", "oI8VCgK", "CM4GDgG", "CMv0Dxi", "Ahr0Chm", "BNn0CNu", "qvrYuxO", "zgXrB0q", "kIG/oLS", "qKzPDwC", "AhjLzG", "EvjyBMu", "C0LJzNa", "mJy0BNfgzuzY", "q3HOBhO", "mJuZmtzguvbKre4", "Aw5N", "z2DLCG", "y2fSBa", "mJyXmJu2mfr3DxvpuW", "ksSPkYK", "ChjVDg8", "kcGOlIS", "DMzRz1q", "BgvUz3q", "y291BNq", "wgDYwgC", "Ag9wr08", "AxmIksG", "AK9gDKO", "CwvhA3m", "y3rVCIG", "mZi0mta0CxzZwen6", "ywn0Aw8", "wL8KxvS", "n1rStNHbCa", "DgvZDa", "CNmUy28", "Aw9UicO", "C3rYAw4", "q3H3t2S", "DuX5Eu4", "iNjLDhu", "v2vHy04", "uwnLBwy", "zxvXCuC", "t2jQzwm", "sw9gyKS", "icH0CNu", "mc05ys0", "Aw5MBW", "Aw5WDxq", "mZC5nJuZm2T1y05bDa", "DhjHy2u", "yK5PtLm", "ELLXuue", "zNzdt1C", "D2HPBgu", "xcTCkYa", "xcGGkLW", "Bg9N", "Aw5PDa", "D2fYBG", "BgvnBfO", "CeHKwuK", "BIGPia", "ndeZody0neTvrxr2yG", "zgzPEgu", "y29UC3q"];
    a0b = function () {
      return F;
    };
    return a0b();
  }
```
And the function that was called many times with a number was none other than the `a0y` one. So these functions were just for decoding the obfuscation. I wouldn't be able to reverse that, so I went to the browser's console and confirm the hypothesis. But upon opening the file in the browser and open the developer tool, the site redirected to `http://pixel.pdfixers.com` and `console.{log,warn,info}` no longer printed anything.
Pretty sure that it had some anti-debugging techniques, so I stopped the code from altering the DOM, and started the debugging process from that.

![breakpoint](/assets/images/pdfixers/breakpoint.png)

Hah! Classic anti-debugging technique.
Now I went to the console and tried to print `a0y` with all possible numbers

```js
for (i = 0; i< 1000; i++) {
    try {
        console.log(i + "|" + a0y(i));
    } catch (err) {console.log("no")}
}
```

And it spitted out some spicy content:

![spicy](/assets/images/pdfixers/spicy.png)

I saved them all in a file, and use a simple python script to replace all that obfuscated `fn(number)` calls with their values.

```python
import re

pattern = re.compile(r'([a-zA-Z]+)\((\d\d\d)\)')

d = {}
with open('str.txt', 'rt') as f:
    for line in f:
        breakdown = line.split('|')
        d[int(breakdown[0])] = breakdown[1].replace('\n', '')
    f.close()
print(d)

def process(x: re.Match):
    return f'"{d[int(x.group(2))]}"'

with open('runtime.js', 'rt') as finput:
    with open('runtime.js.out', 'wt') as foutput:
        for line in finput:
            l = re.sub(pattern, lambda x: process(x), line)
            foutput.write(l)

```

After some trimming and pruning dead branches (it did this a lot to make you confused), I got something at least readable.

```js
(function () {
  var y = function () {
      return function (w, P) {
        var Q = function () {
          if (P) {
            var p = P["apply"](w, arguments);
            return P = null, p;
          }
        };
        return x = false, Q;
      };
  }(),
    E = y(this, function () {
      return E["toString"]()["search"]("(((.+)+)+)+$")["toString"]()["constructor"](E)["search"]("(((.+)+)+)+$");
    });
  E();
  var g = function () {
    var x = true;
    return function (w, P) {
      var Q = function () {
        if (P) {
          var t = P["apply"](w, arguments);
          return P = null, t;
        }
      };
      return x = false, Q;
    };
  }();

  g(this, function () {
    var c = new RegExp('function *\\( *\\)'),
      x = new RegExp('\\+\\+ *(?:[a-zA-Z_$][0-9a-zA-Z_$]*)', "i"),
      w = anti_debugger("init");
    !c.test(w + "chain") || !x.test(w + "input") ? w("0") : anti_debugger();
  })();

  var R = function () {
    var S = true;
    return function (c, x) {
      var w = S ? function () {
        if (x) {
          var P = x["apply"](c, arguments);
          return x = null, P;
        }
      } : function () { };
      return S = false, w;
    };
  }(),
    G = R(this, function () {
      var x = {},
        w = ["log", "warn", "info", "error", "exception", "table", "trace"];
      for (var P = 0; P < w["length"]; P++) {
        var Q = R["constructor"]["prototype"]["bind"](R),
          p = w[P],
          t = Q;
        Q["__proto__"] = R["bind"](R), Q["toString"] = t["toString"]["bind"](t), x[p] = Q;
      }
    });
  G();
  location["href"] = "https://pixel.pdfixers.com";
}());

function anti_debugger(param) {
  var X = {};
  X["vNelr"] = function (b, E) {
    return b + E;
  }, X["ATrQz"] = "ESEWm", X["iMSlb"] = function (b, E) {
    return b !== E;
  }, X["WeacN"] = function (b, E) {
    return b % E;
  }, X["euqqG"] = "debu", X["Qcemf"] = "action";
  var V = X;

  function i(b) {
    var E = {};
    E["vfkgT"] = V["ATrQz"];
    var g = E;
    if (typeof b === "string") {
      if ("Cxhlz" === "Cxhlz") return function () { }["constructor"]("while (true) {}")["apply"]("counter");
      else (function () {
        return false;
      }["constructor"]("debugger")["apply"]("stateObject"));
    } else V["iMSlb"](("" + b / b)["length"], 1) || V["WeacN"](b, 20) === 0 ? function () {
      return true;
    }["constructor"](V["vNelr"](V["euqqG"], "gger"))["call"](V["Qcemf"]) : function () {
      return g["vfkgT"] !== "AYEzW" ? false : true;
    }["constructor"](V["vNelr"]("debu", "gger"))["apply"]("stateObject");
    i(++b);
  }
  try {
    if (param) return i;
    else i(0);
  } catch (b) { }
}
```

Basically what it does is to set up some anti-debugging techniques and set `location['href']` to `https://pixel.pdfixers.com`

## JS RPC?

I repeated the process with the JS code from `https://pixel.pdfixers.com` and found something interesting!

![jscs](/assets/images/pdfixers/jscs.png)

It actually controls the install process by calling functions in the app!

## Conclusion

Right now I only find `InitiateSetup` and `CeaseApplication` got called, but the site has absolute control of the JS code, so they could change the code to execute suspicious functions! I guess that is the reason why all antiviruses didn't mark the file as suspicious because static and behavioral analyses won't find the suspicious activity until the developer makes their moves.

Thank you for reading, and be safe!