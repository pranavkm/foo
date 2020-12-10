I tried build a Razor vsix extension but failed misreably. but there's a fairly easy way to sabotage Razor tooling entirely by messing around with the binaries inside the VSIX.
This should eliminate any design time code generation from the binary.

* Clone the repo.
* Back up "C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\CommonExtensions\Microsoft\RazorLanguageServices\Microsoft.AspNetCore.Razor.Language.dll" and replace it with the file in .\packages\Microsoft.AspNetCore.Razor.Language.dll. It ends up causing the editor to entirely blow up because of version mismatches, but also throws when asked to perform design time code gen.
* Open app1.csproj in VS, F5 to launch it in a debugger, and put a breakpoint in `IncrementCount` on `Index.razor`.
* Click on the "Click me" button. The breakpoint should trigger. See that it works.
* Click on the button again. This time try and edit the incremment amount:

```diff
- currentCount += 1;
+ currentCount += 2;
```

* On saving, notice that the debugger complains about the file sources being different.
* The source generator writes the generated outputs to `obj\Debug\net5.0\Razor\` directory (for diagnostics). For instance, `Index.razor` is written to `obj\Debug\net5.0\Razor\Pages\Index.razor.g.cs`. As you edit in the IDE, notice that the file is updated. You'll notice that the contents for the file do not change when changed with the debugger and after doing a regular build where the debugger happily attaches.
