---
layout: post
title: "[CTF] NahamconCTF Taylor's First Swift writeup"
categories: ctf re swift 
---

## Introduction
This is a RE challenge from Nahamcon CTF. Although this is an easy one, it's interesting because the binary was for macOS ARM64 (mach-O arm64) and as the name suggests, written in [Swift](https://www.swift.org/) programming language.

## Get to know the file
```
rzy@home /r/m/r/f/nahamcon> file taylor
taylor: Mach-O 64-bit arm64 executable, flags:<NOUNDEFS|DYLDLINK|TWOLEVEL|PIE>
```

Since this is an ARM64 binary, IDA Free can't decompile it. The second best thing for this try is Ghidra, I used Cutter (rizin frontend) with Ghidra decompiler but it was not very good at demangling function symbols.

This is the output from Ghidra:
```c
// WARNING: Variable defined which should be unmapped: var_10h
// WARNING: [rz-ghidra] Detected overlap for variable var_9ch

uint32_t _$s6taylor9flagCheckySbSSF(int64_t arg1, int64_t arg2)
{
    int64_t arg2_00;
    uint32_t uVar1;
    undefined8 uVar2;
    undefined8 uVar3;
    int64_t arg1_00;
    int64_t arg2_01;
    undefined8 uVar4;
    undefined *puVar5;
    undefined auVar6 [16];
    undefined auVar7 [16];
    int64_t var_e0h;
    int64_t var_d8h;
    int64_t var_d0h;
    int64_t var_c8h;
    int64_t var_c0h;
    int64_t var_b8h;
    int64_t var_b0h;
    int64_t var_a8h;
    int64_t var_a0h;
    int64_t var_98h;
    int64_t var_90h;
    int64_t var_88h;
    int64_t var_80h;
    int64_t var_78h;
    undefined auStack_68 [16];
    undefined auStack_58 [16];
    undefined auStack_48 [16];
    undefined8 uStack_38;
    undefined auStack_30 [16];
    int64_t iStack_20;
    int64_t iStack_18;
    int64_t var_10h;
    
    arg2_00 = _$ss5UInt8VN;
    uVar4 = _$sSS8UTF8ViewVN;
    uStack_38 = 0;
    auStack_58._0_8_ = 0;
    auStack_58._8_8_ = 0;
    iStack_20 = arg1;
    iStack_18 = arg2;
    auStack_30 = $sSS4utf8SS8UTF8ViewVvg();
    uVar2 = _$sSS8UTF8ViewVABSTsWl();
    uVar3 = $sSaySayxGqd__c7ElementQyd__RszSTRd__lufC(auStack_30, arg2_00, uVar4);
    uStack_38 = uVar3;
    auStack_48 = $sSS4utf8SS8UTF8ViewVvg(arg1, arg2);
    arg1_00 = $sSaySayxGqd__c7ElementQyd__RszSTRd__lufC(auStack_48, arg2_00, uVar4, uVar2);
    auVar6 = $ss27_allocateUninitializedArrayySayxG_BptBwlF(9, arg2_00);
    puVar5 = auVar6._8_8_;
    *puVar5 = 0x73;
    puVar5[1] = 0x77;
    puVar5[2] = 0x69;
    puVar5[3] = 0x66;
    puVar5[4] = 0x74;
    puVar5[5] = 0x69;
    puVar5[6] = 0x65;
    puVar5[7] = 0x73;
    puVar5[8] = 0x21;
    arg2_01 = _$ss27_finalizeUninitializedArrayySayxGABnlF(auVar6._0_8_, arg2_00);
    auVar6 = _$s6taylor10xorEncryptySSSays5UInt8VG_AEtF(arg1_00, arg2_01);
    swift_bridgeObjectRelease(arg2_01);
    swift_bridgeObjectRelease(arg1_00);
    auStack_58 = auVar6;
    auStack_68 = $sSS4utf8SS8UTF8ViewVvg();
    uVar4 = $sSaySayxGqd__c7ElementQyd__RszSTRd__lufC(auStack_68, arg2_00, uVar4, uVar2);
    auVar7 = $ss27_allocateUninitializedArrayySayxG_BptBwlF(0x34, arg2_00);
    puVar5 = auVar7._8_8_;
    *puVar5 = 0x46;
    puVar5[1] = 0x52;
    puVar5[2] = 0x73;
    puVar5[3] = 0x49;
    puVar5[4] = 0x41;
    puVar5[5] = 0x51;
    puVar5[6] = 0x38;
    puVar5[7] = 0x50;
    puVar5[8] = 0x56;
    puVar5[9] = 0x42;
    puVar5[10] = 0x55;
    puVar5[0xb] = 0x56;
    puVar5[0xc] = 0x45;
    puVar5[0xd] = 0x52;
    puVar5[0xe] = 0x45;
    puVar5[0xf] = 0x49;
    puVar5[0x10] = 0x56;
    puVar5[0x11] = 0x45;
    puVar5[0x12] = 0x52;
    puVar5[0x13] = 0x62;
    puVar5[0x14] = 0x42;
    puVar5[0x15] = 0x6b;
    puVar5[0x16] = 0x55;
    puVar5[0x17] = 0x52;
    puVar5[0x18] = 0x46;
    puVar5[0x19] = 0x6b;
    puVar5[0x1a] = 0x55;
    puVar5[0x1b] = 0x49;
    puVar5[0x1c] = 0x42;
    puVar5[0x1d] = 0x78;
    puVar5[0x1e] = 0x56;
    puVar5[0x1f] = 0x51;
    puVar5[0x20] = 0x56;
    puVar5[0x21] = 0x6b;
    puVar5[0x22] = 0x41;
    puVar5[0x23] = 0x59;
    puVar5[0x24] = 0x46;
    puVar5[0x25] = 0x78;
    puVar5[0x26] = 0x4a;
    puVar5[0x27] = 0x66;
    puVar5[0x28] = 0x56;
    puVar5[0x29] = 0x30;
    puVar5[0x2a] = 0x46;
    puVar5[0x2b] = 0x59;
    puVar5[0x2c] = 0x56;
    puVar5[0x2d] = 0x6b;
    puVar5[0x2e] = 0x49;
    puVar5[0x2f] = 0x56;
    puVar5[0x30] = 0x51;
    puVar5[0x31] = 0x67;
    puVar5[0x32] = 0x6f;
    puVar5[0x33] = 0x3d;
    uVar2 = _$ss27_finalizeUninitializedArrayySayxGABnlF(auVar7._0_8_, arg2_00);
    uVar1 = $sSasSQRzlE2eeoiySbSayxG_ABtFZ(uVar4, uVar2, arg2_00, _$ss5UInt8VSQsWP);
    swift_bridgeObjectRelease(uVar2);
    swift_bridgeObjectRelease(uVar4);
    swift_bridgeObjectRelease(auVar6._8_8_);
    swift_bridgeObjectRelease(uVar3);
    return uVar1 & 1;
}
```
I tried another decompiler, known to work really good on macOS: [Hopper](https://www.hopperapp.com/). Fortunately, Hopper does offer a demo version with some limitations (like maximum session time of 30 minutes) but for this small challenge, it was not a big deal. And here's the output:

```c
int _$s6taylor9flagCheckySbSSF(int arg0, int arg1) {
    var_88 = *type metadata for Swift.UInt8;
    Swift.String.utf8.getter();
    lazy protocol witness table accessor for type Swift.String.UTF8View and conformance Swift.String.UTF8View : Swift.Sequence in Swift();
    r8 = Swift.Array.init<A>();
    Swift.String.utf8.getter();
    var_B0 = Swift.Array.init<A>();
    r0 = Swift._allocateUninitializedArray<A>();
    *(int8_t *)var_88 = 0x73;
    *(int8_t *)(var_88 + 0x1) = 0x77;
    *(int8_t *)(var_88 + 0x2) = 0x69;
    *(int8_t *)(var_88 + 0x3) = 0x66;
    *(int8_t *)(var_88 + 0x4) = 0x74;
    *(int8_t *)(var_88 + 0x5) = 0x69;
    *(int8_t *)(var_88 + 0x6) = 0x65;
    *(int8_t *)(var_88 + 0x7) = 0x73;
    *(int8_t *)(var_88 + 0x8) = 0x21;
    r1 = Swift._finalizeUninitializedArray<A>(r0, var_88);
    taylor.xorEncrypt(var_B0, r1);
    var_70 = r1;
    swift_bridgeObjectRelease(r1);
    swift_bridgeObjectRelease(var_B0);
    Swift.String.utf8.getter();
    var_78 = Swift.Array.init<A>();
    r0 = Swift._allocateUninitializedArray<A>();
    *(int8_t *)var_88 = 0x46;
    *(int8_t *)(var_88 + 0x1) = 0x52;
    *(int8_t *)(var_88 + 0x2) = 0x73;
    *(int8_t *)(var_88 + 0x3) = 0x49;
    *(int8_t *)(var_88 + 0x4) = 0x41;
    *(int8_t *)(var_88 + 0x5) = 0x51;
    *(int8_t *)(var_88 + 0x6) = 0x38;
    *(int8_t *)(var_88 + 0x7) = 0x50;
    *(int8_t *)(var_88 + 0x8) = 0x56;
    *(int8_t *)(var_88 + 0x9) = 0x42;
    *(int8_t *)(var_88 + 0xa) = 0x55;
    *(int8_t *)(var_88 + 0xb) = 0x56;
    *(int8_t *)(var_88 + 0xc) = 0x45;
    *(int8_t *)(var_88 + 0xd) = 0x52;
    *(int8_t *)(var_88 + 0xe) = 0x45;
    *(int8_t *)(var_88 + 0xf) = 0x49;
    *(int8_t *)(var_88 + 0x10) = 0x56;
    *(int8_t *)(var_88 + 0x11) = 0x45;
    *(int8_t *)(var_88 + 0x12) = 0x52;
    *(int8_t *)(var_88 + 0x13) = 0x62;
    *(int8_t *)(var_88 + 0x14) = 0x42;
    *(int8_t *)(var_88 + 0x15) = 0x6b;
    *(int8_t *)(var_88 + 0x16) = 0x55;
    *(int8_t *)(var_88 + 0x17) = 0x52;
    *(int8_t *)(var_88 + 0x18) = 0x46;
    *(int8_t *)(var_88 + 0x19) = 0x6b;
    *(int8_t *)(var_88 + 0x1a) = 0x55;
    *(int8_t *)(var_88 + 0x1b) = 0x49;
    *(int8_t *)(var_88 + 0x1c) = 0x42;
    *(int8_t *)(var_88 + 0x1d) = 0x78;
    *(int8_t *)(var_88 + 0x1e) = 0x56;
    *(int8_t *)(var_88 + 0x1f) = 0x51;
    *(int8_t *)(var_88 + 0x20) = 0x56;
    *(int8_t *)(var_88 + 0x21) = 0x6b;
    *(int8_t *)(var_88 + 0x22) = 0x41;
    *(int8_t *)(var_88 + 0x23) = 0x59;
    *(int8_t *)(var_88 + 0x24) = 0x46;
    *(int8_t *)(var_88 + 0x25) = 0x78;
    *(int8_t *)(var_88 + 0x26) = 0x4a;
    *(int8_t *)(var_88 + 0x27) = 0x66;
    *(int8_t *)(var_88 + 0x28) = 0x56;
    *(int8_t *)(var_88 + 0x29) = 0x30;
    *(int8_t *)(var_88 + 0x2a) = 0x46;
    *(int8_t *)(var_88 + 0x2b) = 0x59;
    *(int8_t *)(var_88 + 0x2c) = 0x56;
    *(int8_t *)(var_88 + 0x2d) = 0x6b;
    *(int8_t *)(var_88 + 0x2e) = 0x49;
    *(int8_t *)(var_88 + 0x2f) = 0x56;
    *(int8_t *)(var_88 + 0x30) = 0x51;
    *(int8_t *)(var_88 + 0x31) = 0x67;
    *(int8_t *)(var_88 + 0x32) = 0x6f;
    *(int8_t *)(var_88 + 0x33) = 0x3d;
    r1 = Swift._finalizeUninitializedArray<A>(r0, var_88);
    var_5C = static ();
    swift_bridgeObjectRelease(r1);
    swift_bridgeObjectRelease(var_78);
    swift_bridgeObjectRelease(var_70);
    swift_bridgeObjectRelease(r8);
    r0 = var_5C & 0x1;
    return r0;
}
```

Very good. I would say either decompiler was perfect, but they had their own strength. Ghidra was good at recovering the logic and references between variables, and Hopper was better at demangling symbols. Now we have 2 tools combined to have the best of both worlds, here comes the logic of the program.

## Reversing the logic
As we can see, the `flagCheck` function takes 2 parameters. The function allocates an array of bytes
```c
    *puVar5 = 0x73;
    puVar5[1] = 0x77;
    puVar5[2] = 0x69;
    puVar5[3] = 0x66;
    puVar5[4] = 0x74;
    puVar5[5] = 0x69;
    puVar5[6] = 0x65;
    puVar5[7] = 0x73;
    puVar5[8] = 0x21;
```
=> `swifties!`

and does `xorEncrypt` between this array and the first argument (as you can see in Ghidra's output).

The result of `xorEncrypt` is later compared with another allocated bytes.
```c
    *puVar5 = 0x46;
    puVar5[1] = 0x52;
    puVar5[2] = 0x73;
    puVar5[3] = 0x49;
    puVar5[4] = 0x41;
    puVar5[5] = 0x51;
    puVar5[6] = 0x38;
    puVar5[7] = 0x50;
    puVar5[8] = 0x56;
    puVar5[9] = 0x42;
    puVar5[10] = 0x55;
    puVar5[0xb] = 0x56;
    puVar5[0xc] = 0x45;
    puVar5[0xd] = 0x52;
    puVar5[0xe] = 0x45;
    puVar5[0xf] = 0x49;
    puVar5[0x10] = 0x56;
    puVar5[0x11] = 0x45;
    puVar5[0x12] = 0x52;
    puVar5[0x13] = 0x62;
    puVar5[0x14] = 0x42;
    puVar5[0x15] = 0x6b;
    puVar5[0x16] = 0x55;
    puVar5[0x17] = 0x52;
    puVar5[0x18] = 0x46;
    puVar5[0x19] = 0x6b;
    puVar5[0x1a] = 0x55;
    puVar5[0x1b] = 0x49;
    puVar5[0x1c] = 0x42;
    puVar5[0x1d] = 0x78;
    puVar5[0x1e] = 0x56;
    puVar5[0x1f] = 0x51;
    puVar5[0x20] = 0x56;
    puVar5[0x21] = 0x6b;
    puVar5[0x22] = 0x41;
    puVar5[0x23] = 0x59;
    puVar5[0x24] = 0x46;
    puVar5[0x25] = 0x78;
    puVar5[0x26] = 0x4a;
    puVar5[0x27] = 0x66;
    puVar5[0x28] = 0x56;
    puVar5[0x29] = 0x30;
    puVar5[0x2a] = 0x46;
    puVar5[0x2b] = 0x59;
    puVar5[0x2c] = 0x56;
    puVar5[0x2d] = 0x6b;
    puVar5[0x2e] = 0x49;
    puVar5[0x2f] = 0x56;
    puVar5[0x30] = 0x51;
    puVar5[0x31] = 0x67;
    puVar5[0x32] = 0x6f;
    puVar5[0x33] = 0x3d;
```
=> `FRsIAQ8PVBUVEREIVERbBkURFkUIBxVQVkAYFxJfV0FYVkIVQgo=`

At this point we are pretty sure what the program does and can recover the flag right away. However, for just in case there are something we don't know in the inside, let's take a look at the `xorEncrypt` function.

```c
// WARNING: Removing unreachable block (ram,0x0001000011dc)
// WARNING: Variable defined which should be unmapped: var_20h
// WARNING: Variable defined which should be unmapped: var_30h
// WARNING: Variable defined which should be unmapped: var_10h

undefined  [16] _$s6taylor10xorEncryptySSSays5UInt8VG_AEtF(int64_t arg1, int64_t arg2)
{
    undefined auVar1 [16];
    undefined8 uVar2;
    undefined8 uVar3;
    undefined8 uVar4;
    undefined8 uVar5;
    undefined auVar6 [16];
    int64_t var_d8h;
    int64_t var_d0h;
    int64_t var_c8h;
    int64_t var_c0h;
    int64_t var_b8h;
    int64_t var_b0h;
    int64_t var_a8h;
    int64_t var_a0h;
    int64_t var_98h;
    int64_t var_90h;
    int64_t var_88h;
    int64_t var_80h;
    int64_t iStack_70;
    undefined8 uStack_68;
    undefined8 uStack_60;
    undefined8 uStack_58;
    int64_t iStack_50;
    undefined8 uStack_48;
    int64_t iStack_40;
    int64_t iStack_38;
    int64_t var_30h;
    int64_t var_20h;
    int64_t var_10h;
    
    uStack_60 = 0;
    iStack_50 = arg1;
    iStack_40 = arg2;
    iStack_38 = arg1;
    uVar2 = ___swift_instantiateConcreteTypeFromMangledName(0x100008000);
    uVar3 = _$sSays5UInt8VGSayxGSTsWl();
    $sSTsE10enumerateds18EnumeratedSequenceVyxGyF(uVar2);
    uStack_58 = uStack_48;
    swift_bridgeObjectRetain(arg2);
    iStack_70 = arg2;
    uVar4 = ___swift_instantiateConcreteTypeFromMangledName(0x100008010);
    uVar5 = _$ss18EnumeratedSequenceVySays5UInt8VGGAByxGSTsWl();
    uVar4 = $sSTsE3mapySayqd__Gqd__7ElementQzKXEKlF
                      (_$s6taylor10xorEncryptySSSays5UInt8VG_AEtFADSi6offset_AD7elementt_tXEfU_TA, &var_80h, uVar4, 
                       _$ss5UInt8VN, uVar5);
    swift_bridgeObjectRelease(arg2);
    _$ss18EnumeratedSequenceVySays5UInt8VGGWOh((int64_t)&uStack_58);
    uStack_60 = uVar4;
    swift_bridgeObjectRetain();
    uStack_68 = uVar4;
    auVar6 = imp.Foundation.Data(&uStack_68, uVar2, uVar3);
    uVar2 = _$s10Foundation4DataV19base64EncodedString7optionsSSSo27NSDataBase64EncodingOptionsV_tFfA_();
    auVar1 = imp.Foundation.Data(uVar2, auVar6._0_8_, auVar6._8_8_);
    _$s10Foundation4DataV15_RepresentationOWOe(auVar6._0_8_, auVar6._8_8_);
    swift_bridgeObjectRelease(uVar4);
    return auVar1;
}
```

Swift's high level abstraction is really hard to uncover, however,  we can guess that the function does xor encryption and then call base64 on the ouput.

## Putting everything together
Let's try it with a simple python script

```python
from pwn import xor
from base64 import b64decode

key = b'swifties!'
target = b'FRsIAQ8PVBUVEREIVERbBkURFkUIBxVQVkAYFxJfV0FYVkIVQgo='
raw_target = b64decode(target)

print(xor(key, raw_target))
```

```sh
python solve.py
b'flag{f1f4bfa202c60e2aaa9339de61513141}'
```

## Conclusion
In the end I didn't know the secret behind the md5 hash, but it was pretty fun.
