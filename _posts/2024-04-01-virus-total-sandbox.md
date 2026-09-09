---
layout: post
title: "[Misc] Virus total sandboxes are not reliable"
categories: virustotal software malware analysis
---

## Introduction
This is a quick post showing some interesting findings on VirusTotal. For more context, my friend showed me a PE binary which was marked by some vendors on VirusTotal. Furthermore, sandbox results on VT showed that there were some remote TCP/UDP connections.

## My testing
Here's my trivial program to test. This program prints out feature flags that your CPU supports.

```c
#include <stdio.h>

enum {
	SIMD_SSE = 0,
	SIMD_SSE2 = 1,
	SIMD_SSE3 = 2,
	SIMD_SSE4_1 = 3,
	SIMD_SSE4_2 = 4,
	SIMD_AVX = 5,
	SIMD_AVX2 = 6,
	SIMD_AVX512F = 7,
};

#define SIMD(feat) [SIMD_##feat] = #feat,
char *feature_names[] = {
	SIMD(SSE)
	SIMD(SSE2)
	SIMD(SSE3)
	SIMD(SSE4_1)
	SIMD(SSE4_2)
	SIMD(AVX)
	SIMD(AVX2)
	SIMD(AVX512F)
};
#undef SIMD

unsigned x86_simd(void) {
  unsigned flag = 0;
  int cpuid[4];
  __cpuidex(cpuid, 0, 0);
  int top_level = cpuid[0];
  
  if (top_level >= 1) {
	__cpuidex(cpuid, 1, 0);
	
	if (1<<25 & cpuid[3]) flag |= 1 << SIMD_SSE;
	if (1<<26 & cpuid[3]) flag |= 1 << SIMD_SSE2;
	if (1<<00 & cpuid[2]) flag |= 1 << SIMD_SSE3;
	if (1<<19 & cpuid[2]) flag |= 1 << SIMD_SSE4_1;
	if (1<<20 & cpuid[2]) flag |= 1 << SIMD_SSE4_2;
	if (1<<28 & cpuid[2]) flag |= 1 << SIMD_AVX;
  }	  
  
  if (top_level >= 7) {
	__cpuidex(cpuid, 7, 0);

	if (1<<05 & cpuid[1]) flag |= 1 << SIMD_AVX2;
	if (1<<16 & cpuid[1]) flag |= 1 << SIMD_AVX512F;
  }
  
  return flag;
}
int main() {
  puts("checking your cpu features...");
  unsigned features = x86_simd();
  for (int i = SIMD_SSE; i < SIMD_AVX512F + 1; ++i) {
	if (features & 1<<i) {
		printf("supports %s\n", feature_names[i]);
	}
  }
  return 0;
}
```

Compile with: `cl.exe features.c`

And here's the result on [VirusTotal](https://www.virustotal.com/gui/file/9c92666da0db46f8c0ba80378e9f6bdee41d95d4973f9801382401be1f199eac)

![detection](/assets/images/virustotal/detection.png)

More interestingly, here's the IP traffic from MS Sysinternals Sandbox

![virustotal](/assets/images/virustotal/network.png)

All those IP address are from CDN (Content Delivery Networks) or Microsoft. So it looks like those traffics were from the sandbox itself, not from the binary.