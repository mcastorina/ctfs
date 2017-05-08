# PACTF Write-up (April 2017)
This Jeopardy CTF was in two parts with each part lasting 48 hours. I
had a great [teammate](https://github.com/kilogram) for this, and even
though this CTF is targetted toward high school students, I had a lot
of fun figuring out the challenges with him.

## Part 1 Challenges
Below is the challenge and hint followed by a description of how we
found the flag for each of the challenges in Part 1.

### Et tu, Brute?
> I found a message from Julius. Can you get the flag? Huk aopz pz aol mshn: clup_cpkp_cpjp_TqT0Tq
>
> Look up what a Caesar cipher is. Can you make sense of the encrypted
> text above? Once you do, enter the 'flag' in the text box below and
> check if you’re right!

Simply a Caesar cipher (rot19): `veni_vidi_vici_MjM0Mj`

### Whence I Came
> Look from where I came! The key is somewhere in there… or at least it’d like to be!
>
> I am the creation of open sourcery!

The git repository for PACTF contained the
[flag](https://github.com/PACTF/pactf/blob/pactf/ctfproblems/NOT_THE_SOLUTIONS.md): `WELCOME_TO_PACTF_2017`

### 1597463007
> The number 0x5f3759df is very special, it is the magic behind an
> otherwise rather-annoying mathematical operation. What is the result of
> that operation applied to 0.25?
>
> LMGTFY

As the snarky hint noted, googling the value revealed it as the magic
number for Quake's fast inverse square root function. The inverse square
root of 0.25 (and hence the flag) is simply `2`.

### Substitute Teacher
> Mr. Michael S. "Mike" Rogers is the substitute teacher for the day,
> but he is having trouble deciphering the secret message that was left
> for him by the teacher. Mr. Rogers knows the note is in English, but
> that’s about all. Can you help him?
> [ENCRYPTED.txt](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/ENCRYPTED.txt)
>
> Frequency analysis.

This is clearly a substituion cipher, so I used the online tool
http://quipqiup.com to decode it:

> In cryptography, a substitution cipher is a method of encoding by
> which units of plaintext are replaced with ciphertext, according to
> a mixed system; the "units" may be single letters (the most common),
> pairs of letters, triplets of letters, mixtures of the above, and
> so forth. The receiver deciphers the text by performing the inverse
> substitution. (Wikipedia.org, "Substitution cypher") This is, for your
> sake, a completely normal English text. We were so nice, we decided to
> leave capitalization in the encrypted text... & punctuation! Aren't we
> nice. There is a relatively normal letter distribution in this text, so it
> shouldn't have been too difficult to solve. Anyway, congratulations! Here
> is your flag: `only_slightly_better_than_caesar`

### Dinosaur Never-forget System
> Those Dinosaurs…
>
> The dinosaurs need some way to archive their messages and news for the
> future, so they created the Dinosaur Never-forget System. They wanted
> the login to be public, but they also didn’t want it to be too easy
> to find. So they hid it in a system more antiquated than the dinosaurs
> themselves.
> dinosaurneverforgetsystem.tk
>
> They kept records, too.

`nslookup` and `whois` didn't reveal any information, however `dig`
gave us something.
```bash
$ dig -t any dinosaurneverforgetsystem.tk

;; ANSWER SECTION:
dinosaurneverforgetsystem.tk. 300 IN	SOA	ns01.freenom.com. soa.freenom.com. 1492313780 10800 3600 604800 3600
dinosaurneverforgetsystem.tk. 14440 IN	TXT	"edger entry available at LEDGER subdomain -- flag: dinosaurs_must_stay_informed"
dinosaurneverforgetsystem.tk. 300 IN	NS	ns03.freenom.com.
dinosaurneverforgetsystem.tk. 300 IN	NS	ns02.freenom.com.
dinosaurneverforgetsystem.tk. 300 IN	NS	ns01.freenom.com.
dinosaurneverforgetsystem.tk. 300 IN	NS	ns04.freenom.com.
```

So the flag is `dinosaurs_must_stay_informed`.

### CT S(C)AN
> CHECK THE SAN. MAYBE CT SCANS ARE USEFUL TOO.
>
> We are a security competition, not a hospital. Consult with your
> healthcare professional before deciding to take any CAT scans.

Looking up what "SAN" is revealed it stands for
[Subject Alternative Name](https://en.wikipedia.org/wiki/Subject_Alternative_Name)
which corresponds to alternative certificate names. I then opened up
the certificate details and the flag was in the Subject Alt Name field.

![Subject Alt Name](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/san.png)

### Think of the Cube
> Get Cubed
>
> I found this floating around on a piece of paper while geocaching. What
> could “dqow_UrrEcxbaDdfgwwIhww” possibly mean?
>
> What are some weird ciphers? Maybe originally involving a cube?

After much testing, I found this Geocaching
[website](http://www.geocachingtoolbox.com/index.php?lang=en&page=trifidCipher)
that worked (using the default cube).

```
flag_WowWierdCipherHere
```

### Dinosaur Never-forget System (Continued)
> Those Dinosaurs… had money?
>
> Turns out they also created a ledger system.
>
> I wonder what entry you’re looking for…

From the previous `dig`, we see the dinosaurs had a ledger subdomain,
so we used `dig` for that too.
```bash
$ dig -t any ledger.dinosaurneverforgetsystem.tk

;; ANSWER SECTION:
ledger.dinosaurneverforgetsystem.tk. 14440 IN TXT "3890a940bf54bb50d2ad334d0d0ddbda8a8737b6873277412756724292e89e31"
```

That looks like a hash of some sort, but searching it revealed a BitCoin
transaction page with the flag in it.
```
OP_RETURN 74686f73655f64696e6f73617572735f737572655f6172655f636c65766572
(decoded) jthose_dinosaurs_sure_are_clever
```

### MegaEncryption (TM)
> Personal Advancement of Cuil Therory Foundation
>
> The Personal Advancement of Cuil Therory Foundation (PACTF) left a
> message for [Tony](https://en.wikipedia.org/wiki/User:Tony_Tan),
> but they used MegaEncryption (TM) to encrypt it. What did they
> say? Should we be worried? It seems like they used some sort of public
> medium to send the message…
>
> They kept records!

After much searching, we found the Talk's history page had an entry by
"NOT A CYBERSECURITY COMPETITION," so it was clearly made for a
cybersecurity competition. Looking at the changes revealed a very long
string of characters.
```
Vm0wd2QyUXlVWGxWV0d4V1YwZDRWMVl3WkRSV01WbDNXa1JTVjAxV2JETlhhMUpUVjBaS2RHVkdXbFppVkZaeVZteFZlRll5VGtsalJtaG9UVmhDVVZacVFtRlRNazE1VTJ0V1ZXSkhhRzlVVjNOM1pVWmFkR05GZEZSTlZXdzFWVEowVjFaWFNraGhSemxWVm14YU0xWnNXbUZrUjA1R1drWndWMDFWY0VwV2JURXdWakZXZEZOcmJGSmhlbXhXVm10V1MxUkdWWGhYYlhSWFRWaENSbFpYZUZOVWJVWTJVbFJDVjJFeVRYaFdSRVpyVTBaT2NscEhjRk5XUjNob1YxZDRiMVV4VWtkWGJrNVlZbFZhY2xWcVFURlNNV1J5VjI1a1YwMUVSbGRaTUZaM1ZqSktWVkpZWkZwbGEzQklWbXBHVDJSV1ZuUmhSazVzWWxob1dGWnRNSGhPUm14V1RVaG9XR0pyTlZsWmJHaFRWMFpTVjFwR1RteGlSbXcxVkZaU1UxWnJNWEpqUld4aFUwaENSRlpxU2tabFZsSlpZVVprVTFKWVFrbFdiWEJIVkRKU1YxVnVUbWhTTW5oVVZGY3hiMkl4V1hoWGJFNVVUV3RzTkZVeWRHdFhSMHBJVld4c1dtSkhhRlJXTUZwVFZqRmtkVnBGTlZOaVJtOTNWMnhXWVZReFdsaFRiRnBZVmtWd1YxbHJXa3RTUmxweFUydGFiRlpzU2xwWlZWcHJWVEZLV1ZGcmJGZGlXRUpJVmtSS1UxWXhaSFZVYkZKcFZqSm9lbGRYZUc5aU1XUlhWMWhvV0dKWVVrOVZha1pIVGxaYVdFNVZPVmhTTUhCSlZsZDRjMWR0U2tkWGJXaGFUVlp3ZWxreWVIZFNWa1p5VDFkc1UwMHlhRmxXYlhCTFRrWlJlRmRzYUZSaE1sSnhWVzE0ZDFkR2JITmhSazVzWWtad2VGVXlkREJXTVZweVYyeHdXbFpXY0hKWlZXUkdaV3hHY21KR1pHbFhSVXBKVm10U1MxVXhXWGhYYmxaV1lsZG9WRmxZY0Zka01WcHhVVzEwYVUxWFVsaFdNalZMVjBkS1NGVnRSbGRpVkVVd1ZqQmFZVmRIVWtoa1JtUk9WakZLU2xkV1ZtRmpNV1IwVTJ0b2FGSnNTbGhVVlZwM1ZrWmFjVkp1WkZOTlZrcDVWR3hhVDJGV1NuUlBWRTVYVFc1b1dGZFdXbEpsVmtweVdrWm9hV0Y2Vm5oV1ZFSnJUa1prUjFWc1pHRlNlbXhQVkZaYWQyVkdWblJrUkVKb1lYcEdlVlJzVm5OWGJGcFhZMGhLV2xaWFVrZGFWV1JQVTFkS1IxcEdaRk5XV0VKMlZtMTBVMU14VVhsVmEyUlVZbXR3YUZWdE1XOWpSbHB4VkcwNVYxWnRVbGhXVjNNMVZXc3hjbUpFVWxkTlYyaDJWMVphUzFKc1RuUlNiR1JvWVRGd1NWWkhkR0ZaVm1SSVZXdG9hMUp0VW5CV2JHaERVMnhrVjFadE9WVk5WbkF3VlcwMVMxWkhTbGhoUm1oVlZsWndNMWxWV25kU2JIQkdUMVU1YVZKWVFYZFhiRlpoVkRKR1YxUnJXbFJpVlZwWVZGYzFiMWRHWkZkWGJFcHNVbTFTZWxsVldsTmhSVEZ6VTI1a1YxWXpVbWhWZWtaYVpVWldjMkZGT1ZkaGVsWlFWa1phWVdReVZrZFdXR3hyVWtWS1YxUlZVa2RsVmxKelZtNWtWMDFFUm5oVmJYUnZWakZhUmxkcmVGZGhhM0JRVld4YWEyTXhjRWhpUm1oVFZsaENTMVp0TVRCVk1VMTRWbGhvV0ZkSGFGaFpiWGhoVm14c2NsZHVaR3BTYkhCNFZrY3dOVmRHV25OalJteGFUVVpWTVZsV1ZYaGpiVXBGVld4a1RtRnNXbFZYVm1RMFV6RmFjazVXYkZKaVJscFlXV3RhZDFkV1draGtSMFpvVFdzMWVsWXlOVk5oTVVwMFlVWlNWVlpYYUVSVk1WcHJWbFpHZEZKdGNFNVdNVWwzVmxkNGIyTXlSa2RUYkdSVVlsVmFhRlpxVGxOaFJteFdWMjVLYkZKdFVubFhhMXByVmpKRmVsRnFXbGRoTWxJMlZGWmFXbVF3TVZkWGJXeHNZVEZ3V1ZkV1pEQmtiVkY0VjJ4V1UyRXhjSE5XYlhSM1pWWmtjbGR0T1doV01Ga3lWbTAxYjFZeFdrWlRibHBXVmtWYWVsWnFSbGRqTVdSellVZHNhVlpyY0RWV01XUXdXVmRSZVZaclpGZGliRXB5Vld0V1MxWXhVbGhrU0dSVVZteHdXVmt3Vms5WFIwcEhZMFpvV2sxSGFFeFdNbmhoVjBaV2NscEhSbGRXTVVwUlZsZHdTMU14U1hsU2EyaG9VbFJXV0ZsdGRFdE5iRnAwVFZSQ1ZrMVZNVFJXVnpWVFZqSktTRlZzV2xwaVdGSXpXVlZhVjJSRk1WaFBWM0JUWWtoQ05GWnJZM2RPVmxsNFYyNVNWbUpIYUZoV2FrNU9UVlphV0dNemFGaFNiRnA1V1ZWYWExUnRSbk5YYkZaWFlUSlJNRlpFUms5VFJrcHlXa1pLYVZKdVFuWlhWbEpIWkRGT1YxZHJaRlpoTWxKWFZGWmFjMDVXVm5Sa1IwWldVbXhzTlZsVmFFTldiVXBJWVVWU1ZXSllhSEpXYkZwSFpFWktkR05GTlZkTlZXd3pWbXhrTkdJeVNYbFNiazVVWVRGd1VGWnFRbUZoUmxweFZHeE9hRkpzYkROV01qVnJWVEpLU1ZGcmFGaGhNWEJVVm1wS1MyTnNUbkpoUm1SVFRUSm9iMVpyVWt0U01WbDRWRzVPWVZJeWFFOVVWM2hMVjFaa1dHVkhPVkpOVmtwSVZsYzFTMWxXU2xkalNFNVdZbFJHVkZwWGVITldiR1J6Vkcxb1YyRXpRWGhXVm1NeFlqRlplRmRZY0doVFJYQllWbXRXWVdWc1duRlNiR1JxVFZkU2VsbFZaRzlVYXpGV1kwZEdWMkpIVGpSVWEyUk9aREF4U1dGR2FGaFNNMmg2VmxSQ2ExVXlUa2RXYmtaU1ltMVNWVlZ0ZUhOT1ZsSnpZVWQwV0ZJd2NFaFpNRnB2VjJzeFIxTnNRbGROYm1ob1ZqQmFWMk5zY0VoU2JHUlhUVlZ3VWxac1VrTldhelZYVjFob2FsSlhVbWhWYlhNeFYwWlpkMVpyZEU1aVJuQXdWRlpTUTFack1WWk5WRkpYVm5wV1ZGWnFTa3RUUmxaelZteHdhVmRHU1hwWFYzQkhWakpPVjFSdVVsQldiVkpVV1d4b2IxbFdaRlZSYlVab1RWWndlbFV5TlZOV2JVcElaVWRvVjJKSFVsTmFSRVp6VmpGYVdXRkdhRk5pUm5BMlYxWldZV0V4VW5SU2JrNVlZa1phV1ZadE1WSk5SbFkyVW0xR2FtRjZWbGhaYTFwTFlWWktjMk5HYkZoV00xSm9Xa1JHYTFZeFpISmhSM1JUVFVad1dsZFhlR0ZaVm1SWFYyeG9hMUo2Ykc5VVZsWnpUbFpzVmxkdE9WaGlSMUpLVlZkNGIxWnJNVmRqUjJoWFlrWndVRlpzV2t0ak1WcHlUbFprYVdFd1dYcFdiWGhxWkRBeFYxUllhRmhoTW1oVldXdGtiMkl4Vm5GUmJVWlhZa1p3TUZwVmFHdFVhekZaVVd4c1lWWlhhRXhaYTFwaFZsWktjMXBHYUdoTldFSlJWMVphWVZNeVRuUlVhMVpZWWtkU2NGVnFSa1prTVdSWFZXdDBVMDFXYkRSV1J6VlBXVlpKZVdGRk9WVldNMmhNVmpGYWExWXhjRVZSYlhScFZtdFpNRmRYZEc5U01WVjVVMnRhYWxORk5WZFpiRkpIVmtaU1YxZHNXbXhXTURReVZXMTRhMVJ0UlhwUmJHeFhWa1ZyZUZsVVJscGxSbVJaWTBaYWFWWXlhRzlXVjNSWFdWZFdjMWR1UmxOaVdGSnlWbTE0UzJWc2JGWmFTRTVYVFZad01WVlhlRWRXTWtWNFkwZG9XRlp0VWxOYVZscGhZMnh3UjFwR2FGTk5NbWcxVm14a2QxRXhiRmhVYTJSWFlteEtjMVV3WkZOak1XeHlWMjVPVDFac1NucFhXSEJYVmpBeFJWSnNaRnBoTVVwb1ZqSjRZVmRHVm5WWGJHUk9ZbTFvYjFacVFtRldNazV6WTBWb1UySkhVazlVVnpGdlUyeFplRlZyY0d4U2F6RTBWVEZvYjJGc1NsaFZiV2hXWWtaS1dGWkVSbGRqTWtaR1ZHeFNUbFp1UWpSV1Z6QjRVakZhY2sxV1dtbFNSbHBYV1d0a2IyUnNaRmRYYTNSWVVteGFlbGRyV25kaFZtUkhVMWh3V0ZZelFraFdha3BQVmpGU2RWVnNRbGRpVmtwVlYxZDRiMkl4YkZkYVJsWlNZbFZhYjFSV1duTk9SbGw1VGxaT1YySlZjRWxhVlZwaFZqSktXV0ZGZUZaaE1YQm9XVEo0WVZkWFJrZGFSVFZvVFRCS1NGWnRlR3RPUjBWNFZXNVNVMkpyTlZsWmExcGhWMFpzVlZOc1NrNVNiVkpZVmpKMGQySkdTbk5UYWtaWFZqTm9SRlpxU2t0U01rNUhZa1pvVjAweFNqSldWbEpIWVRGWmVGcElTbWhTTTFKVVZGVmFkMkZHV25STldIQk9WbXMxV0ZZeWVHOWhNVW8yWWtjNVZWWnNXbnBVYkZwelZtMUdSbFJzWkdsV1dFSktWMVpXVjFReGJGZFRhMXBZWWxkb1lWcFhkR0ZOTVZaelYyeGthMUl3TlVkVWJGcHJWR3hhV0dRemNGZGlXR2hVVlhwQk1WSXhaRmxoUm1SWVVqTm9WbGRXVWt0Vk1WcFhZa2hLWVZKNmJGaFphMXAzWld4WmVVNVhkRlZoZWtaWVdXdFNUMVl3TVhGV2JrcFhWa1Z3VEZVeFdrZGpiVVpIV2taT1RrMXRhRlpXYlhoVFV6RktkRlp1VGxOaWExcFlXV3RrVTFaR1VsVlRiVGxwVFZkU1dGWlhkREJWTURGWFlrUlNXazFHV21oWlZWcEtaREZrZFZSc2NGZFNWbTk2Vm1wR1lXRXhXWGhqUlZaU1lsaFNjRlp0ZEZwTlJtUllaRWRHYVUxcldqQldiVFZMVjBkS1NHRkdXbGRpVkVaMlZXdGFWMk5XU25Sa1IzQnBVakZLTmxaclkzaGlNVmwzVFZWa1dGWkZjRmhXYlRGT1pVWnNjVkpzY0d4U2JWSjRWako0VDFZeFNsWmpSemxYVmpOU1dGZFdaRTlqTVZwMVVteFNhRTB4U2xaV2JURTBVekF4UjJKR1dsaGhlbXhZVkZaYWQxTkdXWGxsU0U1WFRXdHdTVlpIY0ZOV1YwVjVWV3M1WVZKRlJqUlZNV1JIVWpKR1IyRkdUazVOYldoU1ZtMHdlRTVIUlhoV1dHaFlZbXMxVjFsclpHOWpiRlYzV2taT1YxSnNTbGhXTWpWUFZERmFjMU51Y0ZoWFNFSllWbXBCZUZKV1NuTmhSbkJvVFZWd2IxWnFTalJaVmxsNFZHNU9VbUpIVW05WlZFNURVMVprVlZOWWFGVk5WV3cxVlcxMGEyRnNTbFZXYmtKYVlrZFNkbFJzV2xwbFIwWkpXa1pPVGxZemFGcFdhMXB2WWpKR2MxTnNhR2hUUlVwWFdXeFNSMVpHV25KWGJVWnFUVmhDUmxWWGN6RlViRnAxVVZoa1YxSnNXblpaYWtwR1pEQXhWMWR0YkZOU2EzQlpWbTB3ZUdJeVVuTlhXR1JZWWxoU1ZWVnFRbUZUUmxwMFpVaGthRlp0VWtsWlZXTTFWakpLV1dGSGFGcGxhM0JRV1hwR2EyUldUblJrUms1cFZqSm9WbFl4WkRSaU1rbDRZa1prWVZKc1dsTlpiRlpoWWpGU1YxZHVaR3hpUmxZMVZHeGFUMVl5U2xaalJXeGhWbGRTZGxadE1VdFNNazVGVkd4d1RtSnRhRFpXYlhoaFVqRmtXRkpyWkZWaVIxSndWVEJXU2sxc1dYaFhiR1JhVmpCV05GWlhOVk5WTWtweVRsWnNXbUV4V21oV01GcFRWakZrZFZwSGFGTmlTRUpIVmxjeGVrNVdWWGROVm1ScVVrVmFXRlZxVG05amJHeFhWMnQwYWsxck5VaFphMXByVlRKS1JtTkZiRmRYU0VKRFZGWmtUbVZHY0VsVGJXaFRUVEpvVlZaR1ZtRmtNa1pIVjI1U1RsWkZTbkZVVlZKWFYwWmFkRTVXVG1oTlZXOHlXV3RhYTFadFNsbGhTSEJWWWtad2VsWnRNVWRTYkZKeldrZHNWMWRGU2t0V01WcFhWakZSZUdKR1pGUmhNWEJaV1d4a2IxbFdjRmhrUjBaT1RWWmFlbFl5ZUd0aGF6RnlUbFZvV0dFeVVucFdha3BHWlZVNVNXSkdaR2xXUlZsNlZsaHdRbVZHU1hsVGEyUmhVbTFTV1ZWcVNtOWlNVnBZWlVkMGFVMXJiRFJXYlRWVFZHeGFObUpHYUZWV2JWRXdWbTE0VjJSRk1WWmFSMmhUWVROQ05sZFVRbTlqTVZsM1RWaEdVMkV5YUdGV2FrNXZZVVpyZVUxVk9WTldhM0I2V1ZWa2IxUnNaRVpUYkVwWFlsUkdNMVY2Ums1bFJrNVpZa1pXYUUxV2NGcFhWM1JyWWpGa1IySklTbGhpYlZKd1ZGWmtVMWRHV2xoa1JFSldUVVJHV0ZsclVsTldNVW8yVm14Q1dsWnNjRXhaTWpGUFVqSkdSMWR0YkZOTmJXaEdWbTF3UjFsWFJYaGFSV2hYWVRKb1VWWnFTalJVTVd4WlkwWmtUMkpHY0hwWGExSlRWMnhhZEZWcmJGWk5ibEpvV1ZkNFQxSnJOVmRhUm5CcFVtdHdTVlp0ZEdGa01WbDRXa2hXVldKSFVuQlZha1pMVG14YWNsa3phR2xOVmxZMFZqSjBZVmRIU2xaWGJHaFhZbFJHVTFSVldsZGpWa3B6WTBkNFYyRjZWalpYVjNScllqRmFSMU5zYkZKaVYyaFpWbTB4VW1ReFpGZFhiazVYVFdzMVNGWXllRzloVmtsNFUyNW9WMUp0VVhkWFZscEtaVVpXV1dGR2FHbFhSa3BaVmtaYVlXUXhUa2RYV0d4c1VucHNXRlJYZEhkVFZscElZMFpPVjFZd1ZqVldWM2hQV1ZaYWMyTkhhRnBsYTFvelZXcEtSMUl5UmtkVWF6Vk9ZbGRvVTFadE1IZGxSVFZJVWxob1YxZEhhR2hWYlRGVFYwWnNkR1ZGZEdwaVJsWXpWMnRhVDJGck1WaGxTR3hYVFc1b2NsWkVSbUZrVmtaeldrWm9hRTFXY0hsV2JYQkNaVWRPZEZSclpHRlNia0pQVlcwMVFtVnNXbkZUYWxKYVZteHNOVlpIZEhOWFIwcElWVzA1V2xaRk5VUlZNVnByVmxaT1dXRkdWazVXV0VGM1ZtMHhNR0V4YkZkVFdHeG9VbnBzVmxadE1XOVNSbHBZWlVad2JGSXdXa3BYYTFwUFZHeFplbUZHY0ZkaE1YQjJXV3BLUjJNeFRuTmhSbHBwVmpKb1dGZFhlRzlVYlZaSFYxaGtXR0pJUW5KVVZscDNaVlpyZDFaVVJsZE5hM0JLVlZab2ExWXhTbk5YYmxwYVlXdHdSMXBFUVhoV01XUjBZa1pPVTJFelFscFdiVEIzWlVkSmVWVnVUbGhpYXpWb1ZXeGFkMk14V25SbFIwWnNZa1p3TUZwVmFHdGhSbHAwVld0b1ZrMVhhRE5XTUZwYVpXeEdjbUZHWkdoaE0wSlJWbTF3UjFZeVVsZFZiazVYWWxkNFZGUldWbmRXYkZsNFdrUkNWMDFzUmpSV01XaHZWMGRLUjFkc1ZsZE5SMUpVVmxWYVlXUkZNVmRVYkZKVFlrZDNNVlpIZUZaT1YwWklVMnRhYWxKWFVsZFVWM0JIWkd4YWNWTnJkRlJTYkZwNVZERmFWMVl5U2tsUmJUbFhZV3RLY2xaSE1WZGtSa3B5V2taV2FWSXhTbmRXVkVKWFVqQTFSMWRZYUZaaE1EVmhWbXBDVjA1R1dsaE9WVGxZVW0xU1NWcFZZelZXYlVWNFYycE9WMDFHY0hwV01HUlRVMVp3Ums5WGJGUlNWWEJJVm14U1MwNUhSWGhYV0dST1ZtMVNjVlZyVm1GWFJteHpZVWM1V0ZKdGVGaFdNblF3WVRGYWNsZHFSbGhoTVhCeVdWVmtSbVF5VGtsaVJtaFhUVEJLU1ZaclVrZFhiVlpIWTBWc1ZHSlhhRlJVVkVwdlZsWmFSMVp0Um10TmExcElWakkxVTJGc1NsaFZiRlpWVm14VmVGWXdXbHBsVlRWWFZHMW9UbFpYT0hsWFYzUmhZVEZhZEZOc2JHaFNSVFZXVm14YWQyRkdXWGRhUlRsUFlrWndlbGxWWkhOVk1rcHlVMnRzVjJFeVVUQldha1pXWlVaa2RWTnJOVmhTYkhCMlZsUkNWMlF4YkZkalJtaHJVakJhYjFWdE5VTlNNVmw1WkVSQ2FHRjZSbGhaTUdoelZsZEtSMk5JU2xkU00yaG9WakJWTlZOWFNrZGFSMnhYVWxac05sWnNaSGRUTVU1MFZteGtWMkpIZUc5VmJURnZZMFpzY2xadVpGZFdiSEI0VlZjd05WVnJNWEppUkZKWFlsUldTRlpYTVV0V2JHUjBVbXhrYVZkSFozcFhiRlpoV1ZaT1NGWnJXbEJXTW1oVVdWUk9RbVF4WkhOV2JVWm9UVlpzTTFSV2FFZFdNa3B5WTBab1YyRXhXa2hVYTFwYVpVWmtjbHBIY0dsVFJVcEpWbTB4ZWsxV1dYbFRia3BVWWtad1dGbHNVa2ROTVZZMlVtczFiRkpyTlRGV1YzaFhZVVV4VjJOR2JGaFdNMUp5VmxSR1lWSXhWbk5XYkZKcFZrZDRkMVpHV210Vk1XUkhWMnhvYTFKRlNsZFVWVkpIVjBac2NsVnNUbGROVlc4eVZtMTRRMWRzV25OalJYaGFUV3BHVUZVd1ZUVldNVnB6V2taT1YySnJTazlXYlRGM1VqRnNWMkpHWkZSWFIyaG9WVEJhUzFaR2JITmhSazVWVFZkNGVWWnROV3RVTWtwSVZXeGFWbFl6YUhKV2FrRjRWakZhY1Zac1dtbFNNbWd5VjFaa05GUXhTbkpPVm1Sb1VtNUNjRlZxU205V1ZscEhWV3RrV0dKV1dsbFdiWFJ6VmxkS1NGVnVRbFpoYTFwTFdrUkdZVkpXU25Sa1IyeE9WbTEzTVZaWGVHOWpNa1Y1Vm01S1dHSkhlRmhaYkdodllVWndTR1ZJWkZOV2EzQjZXVEJrTkZVeVNsZFRiVVpZVmtWS2NsbHFSbUZTTVU1ellrWkthVkl6YUZsWFYzaHZWVEZrUjFwR1pHRlNiVkp4VkZaa1UwMVdWblJsUlRsb1ZtMVNTRlV5TlhOV01rcFZVbXBhVlZadFVsaFpla1ozVTFaT2RGSnNVbE5XUmxveVZteGtORlV4UlhkTlNHaFhWMGQ0Y1ZWcVFtRlhSbEpZWkVoa1ZGWnNjRWxaTUZVMVYyeGFjMk5HYUZwTlJuQnlWMVphWVZZeVRrVlViRnBwVjBkbmVsWlhjRXRTTVVsNVVtdGtWV0Y2Vms5WmJHUnFaVVphZEUxVVVtaE5iRVkwVld4b2IyRldTblJoUmxaWFRVZFNkbFpxUm5OamJHUjFXa1prVGxZemFGZFdWRW8wVkRGa1IxTnVUbFJpUjJoWVZGYzFVbVF4YkhKYVJYUlVVakZhU0ZkcldtOVhSazVHVTJ4c1YySllRa05hVlZwTFZqRk9kVlp0YkZOaWEwcFFWbGN3TVZGdFZuTlhia1pVWVd4S1lWWnRNVk5UVmxaMFpFYzVhRkpyY0RCV1Z6QTFWMnhhUms1VlVsWk5WbkJ5Vm14YVQxZFhSa2RXYXpWWFYwVkdNMVp0TUhoTlIwVjRZa1prVkdKR1dsaFphMXBMVm14YWRHVkhSazVOVmxZMVdsVmtSMkZyTVZsUmEzQllZVEZ3VUZaSGVGcGxiVVpIWTBaa2FFMVlRakpYV0hCSFZtMVdWMU5zYkdsU2JIQndWbXBLYjFkR1pGaGtSMFpVVFZkU1NGWXlkRmRWTWtwV1YyNUdWVll6VW1oVmFrWmhVMGRXU1ZwR1pFNWhNMEpMVmxaa2QxVXhXWGhYYWxwU1lXczFWbGxyWkZOU01YQkZVVmhvVjAxWFVqQlphMXBQVlRKRmVsRnNjRmRpVkVJeldsVmtTbVZXVm5WVWJHaFlVakZLV2xkWGVHOVZNVnBYVm01R1VtSlZXbFZWYlRFMFpWWlplVTVYZEdoU2JIQXdWbGQwYzFkc1dsaFVWRVpYWVd0d1RGWXhXa2RqYlVaSFkwZDRhRTB3U2xKV01XaDNVakpGZVZWc1pGVmliRXB5Vlcxek1XTkdXbkZUYlRsWFlrWnNORmRyVWtOV01ERnpVbXBTV0dFeFduSldNR1JMWTJ4a2MxWnNaR2hOVm5CdlYxZHdSMVV4V1hoalJXUlZZa1pLVkZsclpETk5WbHBJWlVaYVQxWXdXa2xWTW5SaFlXeEtkR1ZIUmxkaVJuQXpXbGQ0WVdOV1JsbGhSbEpUVFVad1NGWnNaRFJaVmxKelUyNVdVbUZzY0ZoWmJHaERVMFp3UlZKcmRHcE5helZIVmpKNFExWXdNWFZoUmxaWFlXdEtXRlV5YzNoak1XUlpZVVpvYVZJeFNtaFdiWEJEVmpBMVYxWnVVbXhTTTFKWVZXMHhVMU5XY0ZaWmVsWlhZbFZXTkZZeWNFOVdNREZIWTBod1dtVnJjRWhWYlhoVFl6RmFjMVJ0YkZOaVdGRXhWbTE0YW1ReVZrZFRXR2hoVWxkU1ZWbHRkSGRWUmxsM1drUlNXbFpzY0hwV01qVnJWVEZLYzJOSWFGWk5ibEYzVmxSQmVHTnJOVlpoUm5Cb1RWaENlVlpzVWt0VE1VcDBWR3RhVGxadGFGaFVWRUpMVTFaYWMxVnJaRmhpVmxwSVdUQldjMVpIU2xaWGJGSmFZa1p3VEZwRVJtRmtSMVpJVW0xMFRtRXhjRWxXYWtreFZURlNjMXBGYkZKaGVteFlXV3hvVTJWc1VuSlhiVVpZVWxSV1YxUXhXbTlWTURGSlVXMUdWMkpIVVRCWmFrWmFaVlpLZFZOc1VtaGhlbFpaVmtaYVlXUXdNSGhYYmxKc1VqTlNjVlJXV2t0bGJHeFdXa2hPVmsxcmNIbFZNblJ6VmpBeFdGVnFUbGRTUlhCVFdsVmFhMk14Y0VkaFJtaFVVbFZ3TWxac1pIZFRNa2w0WWtaa2FsSnRhSEpWYWtKaFZERlNWMWR0Um14aVJtdzFXa1ZrTUdGSFNsWlhWRXBYWWxob2RsWnFSa3RPYkVwMVkwWmthRTFZUWsxV2JUQjRVbTFXZEZOclpHRlNNbmhZVld4YWMwNXNXbk5oU0dSU1lsWmFTRlV4YUhOV1IwVjVWVzFvVm1KWWFETldNVnBYWTFaT2RFOVdhR2xTTVVwYVZrWmFVMUV5UlhkTlZtUnFVbTFvWVZsVVNtOVVSbGwzV2tWMGExSnNXakJWYlhocllWWmFSbFpZWkZkaVZFVXdWbXBLVTFKck5WZGhSM1JUWWtad2RsZHNaREJaVm1SWFdrWldVbUpVYkhGVVZscHpUVEZTVjJGRlpGWk5hMVkxV1ZWa1IxWXlSWGhYYldoV1pXdGFNMVp0ZUd0a1IxSklZMGRzVjFadVFrcFdNVnBYWVRKSmVGVnVVbFJoTVhCd1ZXdGFkMWRHVWxkWGJtUm9VbTFTV0ZZeWN6RmlSa3AwVlc1d1dHRXhjSFpaVmxwS1pXMUdTRTlXWkZkbGEwa3dWbXhTUjJFeFNYbFVhMlJvVWpOU1ZGbHRkRXRWUm1SWVpFZEdhMDFWTlVoV01XaHpWR3hhV0ZWc2JGWmhhMG96VmpCYVdtVlhVa2hQVm1oVFRVWlpNVmRXVm1GaU1WbDVVMnRvVm1FemFGaFVWV1JUVTBaV2RHTjZWbGROVm5CNlYydGtOR0ZXU25SUFZGWlhZbFJHTTFWcVJuTlhSa3BaWVVkR1UxWXlhRmxYVmxKTFZURmFjMkpJU2xkaWJWSldWRlphWVdWV1duUmtTR1JWWWtWd2Vsa3dhSGRYUjBWNFUyeFNXbFpXVmpSV01GcFhZMjFLUjFkdGJGTk5iV2Q1VmpGYWEwNUdVWGhUYmtwUFZtMVNiMVZ0TlVOalJsWnhVMjA1VjFac2NFaFdWM1JyWVRBeFdGVnJiRmRpV0ZKMldWUkdTbVZzUm5WUmJGcG9ZVEZ3VFZaSE1UUlhiVlpZVld0b2ExSXllRTlXYlhoYVRVWmFjVk5xUWxwV2JWSkpWVEo0WVZkSFNraGhSbWhhWWtaS1NGWlVSbE5XYkdSeldrWmtWMkV6UWpaWFZFSnJUa1paZVZKcVdsZGhiSEJZV1d4U1JrMUdiRlZUYTJSVVVtdHdlRll5ZUd0aFZsbDVZVVp3V0ZZelVuSldWekZTWlVad1IxcEhSbE5XTVVwVlZrWmFhMVV3TVZkV2JsSnNVbFJzYjFadE1WTlRSbFY1VGxoT1YwMXJjRlpWYkZKRFZtMVdjbU5HYUZwbGEzQk1WV3BHYTJSR1NuTlZiRTVYVW14d1lWWnRkR0ZaVm14WVZWaGtUbFp0VWxoWmEyUnZZekZXY1ZSc1RsaFNiRXBaV2tWb2ExWkdXbk5qUld4YVRVWndVRlpxUmxwa01WcHhWV3h3VGxKc1ZYaFdNVnBoV1ZaWmVWUnJiRlJpUjFKUFZtcEdTMU5XV2xoalJYUlRUVlUxU1ZVeWRHOWhWa3B5VGxac1ZtSkdXak5XYTFwaFpFVXhTVnBIZUZOV1JscEpWbTE0YjJNeFdsZGFSV2hvVWpKb1YxbHJaRk5XUmxsM1YyNU9hMUl4V2tkYVJXUjNZVlpLYzFkWWNGZGlXR2hVVldwR1lXTXhUblZVYkdob1lUQndiMVp0ZUd0aU1WWkhWMnRXVTJKVlduSldiWFJoWlZaa2NsZHVaRmROVm5CNlZteG9iMVl5Um5KVGJrcFdaV3RhUzFwV1dsTmpiVXBJWTBaT2FXRXdjRkZXYkdOM1RWWkZkMDFZVGxoaWExcFZXVzAxUTJNeFZuUmtTRTVQVW14d1NWUnNWVFZXYlVwV1kwVnNWMVo2UVRGV01uaGhVbXhPZEZKc1ZsZFdia0p2Vm1wQ2ExUXhTblJWYTJSWFlYcFdXRll3VmtkTk1WcDBUVWhvVGxJd1ZqUlphMXByVmtaa1NHVklUbFppV0doTVYxWmFjMk15UmtkVWJGWlRZa2QzTVZkc1ZtRmtNa3BIVTI1T1dHSnRlRmxaVkVaM1RURmFjVk5yZEZOTlYxSldWVzE0YTJGRk1WbFJha3BZVmpOQ1NGbDZSbHBsUmxaMVUyeG9hVmRGU2xWWFZsSkRaREZrVjFwR2FHeFNhelZVVkZaYWQxTkdiRlZVYlhSWVVqQndTRmt3YUV0V1ZscHpZMFJPWVZac2NETlZiWGhyWkZad1IxcEZOV2hOTUVwTVZteGFhMlF4VlhoWFdHaFlZV3h3VkZaclZrdFhSbFp5Vm0xR2FGSnNXakJaZWs1dlZqSktSMk5JY0ZoaE1sSjJWa2N4UzJSSFJrZGpSbVJwVmtWR05GZHJVa2RYYlZaSFdraEtZVkp0YUhCWlZFWjNWbXhrVjFWck9WWk5WbkJZVm0wMVUySkdTWGRYYms1YVlURndNMVJWV25kV2JVWklaRWRvVTJKSVFYZFdiR1F3WWpGYWNrMVdhR2hTUlRWWVZGVmFkMWxXY0ZobFJXUnFZbFZhU0ZkclpHOWhSVEIzVTJ4YVYySkdTa1JYVmxwV1pWWlNjMXBIUmxSU1ZGWlhWMWQ0YTA1R1pFZFZiRlpVWWxSc1YxWnRlSGROUmxKWFlVZDBWMVpVUm5sWmExSlRWakF4V0dGSVdsZE5SMUpNV1RJeFQxTkhSa2RYYldoT1ZtNUNUbFp0TUhoT1JsVjRXa1ZvVm1FeWFGUlphMlJUVkRGYWRHUklaRmhXYkhCNFZURm9iMVJzU1hkWGEyeFdUVzVTYUZaRVJtRlhWa1p6V2taa2FHRjZWakpYYTJRMFdWZE9WMVJ1U21GU00wSnZXbGQ0WVZkV1duTmFSRkpvVFZac05GWXlOVmRWYlVwVllrWnNWMkV4V2pKVVZWcHJWakZrZEdSR2FGTmhNMEkxVmpKMGExSXhWWGhYYTJSWVlrZDRXRmxYZEV0WFJsVjVZek5vVTAxWFVqRldWekUwVlRBd2VGTnNSbGRXTTBKRVdYcEJNVll4V25WVmJYUlRUVVp3VjFkWGRHdFZNVTVIVmxoc1RsSkdXbGhaYkZaM1YyeGFXRTFYT1ZkTlJFWklXVEJhVTFZeVJuSlhia3BoVW14d00xVXdaRWRTTVZKMFlrWk9hVkl6VVRCV2JURXdXVlpzVjJKR2FGTlhSM2hVV1cxMGQxUXhXblJOVms1cVZteGFlVlp0TVVkWFJrcHpZMGhvVjFKNlFURldWRVpMWXpKT1NXRkdXazVpYkVZelZtMTBZVmxYVG5OYVNFWlRZa2RvYjFSV2FFTmxWbVJYVm0xMFUySldSalZXUjNSdlZVWmFkRlZzYUZaaVIxRjZWRlZhWVdSSFRrWmtSbFpwVm14d1NWZFVRbGRqTVZsNVUydGFUMWRGU2xkWmExcExWMFp3UlZKdFJtdFNNVnBKVlcweE1GUnNXWGhUYTJ4WFlXdHZkMWxVUm5OV01WSnlZa1pLYVdGNlZsaFhWM1JoVXpGa1IxWllaRmhpYXpWWVZtMTRTMDFHY0VaWGJYUlZZbFZXTmxWWGNGZFdNVWw2WVVoS1ZtRnJXbUZhVmxwUFl6SktSMWR0YkdsaE1IQllWakZrTUZsWFVYbFdiazVZWW14S1QxWnNaRk5XUm14eVYydDBiR0pIVW5sV2JGSkhZVVpLVlZGcVRsWk5ibEYzVm1wQmVGWXlUa2RoUm1Sb1lURndWRmRzVm10VE1VbDRZMFZrVm1KWGFFOVdNRlpMV1ZaWmVGa3phRTVTTVVZMFdXdGFhMkZzU25SaFNFcFdZV3RLYUZscVJsZGtSVEZWVkdzMWFWSnNiM2RYYkZaV1RsZEdTRkpxV2xOaVIyaGhWRlZrYjJWc1duTmFSWFJUVFdzMVNsVXllR3RWTURCM1RrUkNXR0V4V25KVmFrWlBVakZPZFZSdFJsTk5ibWhhVmxkd1MySXhXbk5YYms1b1UwZFNVMWxZY0VkWFJscElaRWQwVjFZd2NFbGFWV00xVm0xS1ZWSnVXbHBOUm5CNldUSjRZV1JXY0VkYVIyeFRUVlZ3WVZacldtRlZNVlY0V2toT1dHSnJOVmhaVjNoTFdWWnNWVk50T1ZaU2JWSllWakowTUZReVNsWmpSV2hhVmxad1dGWnFTa3RXTVVwWldrWmtVMkpHY0c5V2JGSkxWRzFXUjFwSVZsUmlSVXBZV1d4YVMxWldaRmRWYXpsU1RVUldlbGRyV2xkVmJVcElWVzA1VlZac2NIcFVWRVpUVmpKR1JscEdaRTVoTVZreVYxWldhMUl4V1hsU1dHaFlZVEpvVmxsVVJuZFpWbkJGVW0xMGFrMVlRa2xhUlZwclZHeE9SbE5yY0ZkTlZuQllXVlJLU21WR1ZuVlViVVpUVm01Q1ZsWnFRbXRPUm1SSFlraE9hRko2YkZoVmJYaGhaVVpXZEU1Vk9WZE5SRVpZVlRJMWMxZHNXbGRqU0VwYVZsWldORmw2Ums5a1ZuQkhXa1prYkdFd2EzZFdiWGhUVWpGV2RGWnJhRlJoTW5oWFdXeG9iMVpHV1hkWGEzUnJUVlp3U0ZZeU1VZGhNREZ6VjJ4b1YwMXVhSFpaVkVaS1pXeFdjMkpHV21oaE1HOTZWbXBDWVZkdFZsaFZhMmhRVm0xU1QxWnNZelJsVmxwMFRWUlNVazFXYkROVVZsWnZWbTFGZW1GR2FGVldWMUpVVmpCYVYyUkhUalpXYkdST1ZtNUNObFl5ZEdGVU1WbDRXa1ZhVkdGNmJGaFpiRkpIVFRGV2NWSnVUbGROYTNCSVdWVmFZV0ZGTVhSaFJtaFlWbXhLVEZaVVNrZFNNVnAxVldzNVYxWlVWbHBXVnpCNFRrZFdjMWRZYkd0U1JVcFlWVzE0ZDAxR1dsaGxTRTVhVm10d1dWWkdVa2RXTVZwR1VtcFNWMkZyV21oWk1WcGhZekZrY2s1WGJHbFNXRUV4Vm1wR1lXRXdOVWRWV0doVVltdHdVRlp0TVZOaU1WWjBUbFZPYWxKc1duaFZNakExVkcxS1NGVnNhRmRpVkVaSVZtMXplRll5U2tWVmJHaG9UVzFvVVZacVFtdFRNV1JZVW10a2FGSnRVbTlVVmxwM1lqRlplRmR0UmxwV01EVjZXVEJXVjFadFNrbFJiR2hhWVRGYU0xUnRlR0ZqVms1VlVteGFUbUV4V1RGV2JUQXhVekZrU0ZOcmFHeFNiV2hoV1d0a2IyUnNiRlpYYm1SVVVqRmFTbFpIY3pWaFZrcFpVV3BhV0ZaRlNuSlVhMXBXWlZaT2MxcEdVbWxXVm5CYVZtMTBWMU15UmtkV2FscFVZVEpTV0ZSV1ZURmxiRmw1VFZoa1ZrMUVSakJaVlZwM1ZqSktWVkZxVWxkV2VrWlRXbFZrVDFKc1duTlViV2hzWWxob1ZsWnJXbUZaVjFGM1RWWmtWMkpzU25OVmJGSnpZakZhZEUxV1RsUlNiRlkxV2xWb2ExWXdNWEpqUmxwV1ZtMW9kbFp0ZUdGU2JHUnpVV3hhYUdFeGIzcFdWRVpoVkRKU1IxTnVUbFZpV0dod1ZqQmtiMWRXV25GU2JYUlBVbXhzTlZaV2FHOVdSbVJJWVVjNVZWWkZjRlJXYWtaelYxZE9TVlJzYUZOaVJYQllWbXhrTUdFeFpFZFhiazVxVW0xb1lWWnNaRkprTVZsM1drVjBVMDFyTlVkWGExcGhZVlpKZVdGRVNsZGlWRVkyV2xWYVNtVldVbkpoUjJoVFRVWndWVmRYZEc5Uk1WcFhXa2hLVjJKVldsVlVWM040VGtaYWRFNVZPVmRXTUhCSldWVmFVMVl5U2toaFJWSmFZV3RhYUZwRlpFOVNiSEJJWVVVMVUxSnNjRXBXYlhCSFlqSkplVkpzWkZSaWJFcFhXVzEwUzFZeGJITlZhMlJYVW14c05GWXlOVTloTVVsM1RrUktWbUpZYUhKV1IzaGFaVzFHUjFwR1pGZFNWemgzVm10U1MxSXlUbkpOVm1SaFVqTkNWRlZzVm5kaU1WcDBaVWRHV0dKV1JqUldNV2hyVjBkRmVsVnVSbFZXTTAxNFZUQmFjMk5zY0VaYVJtUk9WbTEzTUZaWE1YZFVNVmw1VTJ4c1VtRXdOV2hXYkdSVFZVWndSbGRzVG1waVNFSkpWVzE0VDJGWFJYZGpSa1pYWWtkT00xUldXa1psVms1WllVWm9hV0pGY0ZCV2JYUmhVekZrVjJKSVRsaGlWVnB6V1d0YVlVMUdVbk5XVkZaWFRXdHdWMWt3Vm5OV01WbDZWRlJHVjJKVVJreFdiRnBoWTJ4V2MyTkhhR2hOV0VJMVZtMXdSMkV4VVhoVGJsSldZVEpTV0ZsWWNGZFhSbHB4Vkcxd2JGSnNjSHBYVkU1dlZHeEpkMVpxVWxkTlYyaDZWbXRrUzFOSFJrbFViRnBwVmtWVmQxWnRjRWRqTVdSSVZXdHNZVkl6VW5CVmJURXpUVlprYzFadFJtdE5hM0JJVmpGb2IxWkhSWGxoUm14YVZrVmFNMWt5ZUhOV2JIQkhXa2R3YVZJeFNqWlhWM1JoWVRGU2RGTnVUbFJpUjNoWlZtMTRTMU5HWkZkWGF6bFRUVlpLTVZadGVGTmhSMVowWlVac1dGWXpVblpWVkVwSFVqRndSMXBHV21oTk1VcFlWa1phWVdReFRrZFhXR3hyVW0xU1YxUlhkSGRXTVZsNVRsVmtWMVpzY0RCV1ZtaDNWbFpaZW1GSGFHRlNWMUpRVkcxNFlXTXhjRVpPVjJoT1RVVndUVlp0ZUdGaE1sRjRWbGhvV0ZkSGFGbFpWM2gzVlRGc1dHVkZkRmRpUm5CNlZqSTFhMVl4U25Sa2VrcFdZbGhOTVZsVVJtRmpNV1J4VVcxR1YxWnVRbEZYVmxwaFlURk9TRkpyWkdsU2JWSnZWRlpvUTJWV1pISlZhMlJZWWxaYVNGZHJhRXRaVmtwMFlVaENXbFpGV2pOV2JYaHJWakZXY2s5V1drNVdia0paVm0wd01WVXlTa2RUV0dSWVltczFZVmxVU2xOV1JuQlhWMnh3YkZaVVZsbGFSV1J2VlRKS1NWRllaRmRpUjFGM1dXcEdZV05yTVZsU2JFcFhVbFp3V1ZkV1pEQmtNVkpIVjFob1dHSkhVbkpWYWtKaFUwWnJkMXBFVW1oU1ZFSXpWVEo0UjFZd01YVmhSbWhhVmtWYVUxcFZXazlrVmxaelZHMW9UbUpGY0ZWV01XUXdZVEZWZVZSclpGaGlhMXBWV1d4U2MxZEdiSEpYYms1UFVtMVNlVlpYZEU5V01ERkZVbTV3Vm1KWVFsUldha0Y0VjBkR1NWZHNaRTVTTVVwTlZsZHdSMkV5VFhoWGJrNWhVbFJXVkZSV1ZuZFdiRnAwWlVaa1dsWnRlRmxXUjNoclZUSktjazVYYUZaaVJrcDZWbXRhYzJOc1pITmpSVFZYWWtoQ1NsZHNWbXRTTVZwV1RWWmthVkpHV2xkVVZ6VnZZVVpzV0dWRmRGaFNNVnBKVlcxNGEyRldTblZSV0hCWVlUSlJNRmRXWkU1bFJuQkpWRzF3VTAweWFIZFdWM2hoWkRBMVYxZHVTbUZTUmtwWVZGWmFjMDVXVlhsa1NFNVdZbFZ3VmxsclduTldNREZJWVVWU1ZrMUdjSEpXYWtaclpFZFNSMkZHVG1oTk1Fa3hWbXBLTUdFeVNYaFdXR3hVWWtkU1ZWbHRlSGRYUmxaMFpVaGthMkpHV2xsWmVrNXZWVEF4V0ZWcVJsZFNNMmgyV1ZaYWExSXhUbk5TYkdSWFRUQktSVlp0Y0VKTlZrbDRXa1pzYUZJeWFGUlVWRVpMVmpGa1dHUkdXazVXYXpWWVdXdGFZVmRIU2toVmJHeGFZVEZ3ZWxSdGVHRlhSMDVHVDFaa1RsSkZXa2xYVkVKdlpERmtjazFZVm1oVFJYQm9WV3hhZDFSR2NFWmFSV1JUVFZaS01GbHJXbTlWTURGMFlVWndWMkpVUlRCWlZ6RlhVbXN4Vm1GR1dsaFNNMmhXVjFaU1MxVXhaRmRpU0ZKcVpXdGFXRlp0TVRSTlJsSlhWV3hPV0ZKc2NEQmFWVnBoVm1zeGRXRkdRbGRTTTA0MFZtcEtUMUl4WkhOWGF6VnBZa1ZaZWxadGVGTlJiVlpJVkc1S1QxWnNjRzlWYlRWRFlqRlNWMkZGVG14V2JIQjZWbGQwTUZaRk1WWmpSRUpYVWpOU2FGWnFTa3RUUmxaellrWmFUbEpzY0UxV1ZFWmhXVmRPYzFadVNtRlNiRXB3Vm0xNGQxTnNXbkphUkVKYVZqQmFXRlV5TlU5WFIwcHlVMnhrV2xZelVtaFdSVnAzVWxaS2RGSnRkRk5OVm5CS1ZsY3dlRTFHVW5SVGExcHFVa1p3V0ZsWGRIWmtNV3hWVW0xMFZGSnJOWHBXTW5odllWZEZlbEZzWkZkaVZFSTBWbFJHWVZJeFdsbGFSbWhwVTBWS2VsWnRjRWRUTVU1WFZWaG9XR0pIVWxaWmExcDNUVlpXV0dOR1pGZE5WV3cyV1ZWa2IxZHNXa1pYYldoaFVsWndVRmw2U2t0VFIwWkhWV3hTVTFaWVFsbFdiVEI0VFVaWmVHSkdaR2xTYlZKeFZXMHhVMWRHV25KV2JVWllVbTE0ZVZZeWREQmlSMHBJVlc1c1YySkdTa2haVlZwTFZsWkdjVlJzV2s1aGExb3lWbTF3UzFNeFpGaFNhMnhVWWxWYVZGVnNXbmRXUm1SelZtMUdhRTFzU2xoV1J6VkxWREZhZEdGR1VscGhNbWhFVkZkNFlXTXhjRWxhUmxKT1lUSjNNRlpxUm05aU1rWlhWR3RhYWxKdGVHRlpiRkpIVmtaYWRHVkdaR3RTVkZaWFZrZDRUMVJzV1hoVGFsWlhZV3R3TmxSc1dtRmphekZXVm14T2FXSllhRmRYVjNSaFdWZFNjMWRZWkdGU1ZuQnpWVzE0ZDFkc2JGWlhiWFJYVFZWd1dsVlhjM2hYUmxwR1lucENWMUpGY0ZCYVJWcFhZMnh3U0ZKc1RrNWliV2hoVm14a2QxTXlTWGhYYms1WVltczFhRlV3WkZOV1ZsSlhWbFJHVGxKc2NEQmFWV1F3VmpGSmQyTkZiR0ZXVjAweFZtMHhSMk5zVG5SaFJtUm9UVmhDYjFkVVJtRlVNbEpHVDFaa1lWSnRVbkJXYlhSM1dWWmFjbGR0UmxaTlZWWTFWV3hvYTFSc1duUmhSbVJhWVRKU2RsZFdXbk5XVms1MFQxWldVMkpXU2xsV2Frb3dUVVpWZDAxV1drOVdSVXBYV1d4b2IyTnNiRmRYYlhSVVVtMDVObGt3V205aFZrcDFVVzA1VjAxV2NISldWRVpMWTJzMVYxcEhhRk5OTVVwVlYxZDRZVk15VW5OYVNFNWhVa1ZLWVZadE1WTlhSbGw1WlVaa1YxSXdXbnBWTWpBMVZsWmFjMk5JY0ZWV1JWcFVWbXBLUjFKc2NFZGFSVFZvVFRCS1NWWnNVa3BsUmtsNFYxaHNWR0pzV2xOWlZFcHZWMFpXYzFkdVpGaFNia0pIVm14b2IxWXlSalppUm1oWFVqTk5lRlpxUVhoV2JVbzJVMnhrVGxacmNFVlhWbEpMVTIxV1IxWnVWbFZpVlZwVVZtMDFRMVpzWkZkVmF6bFNUVmRTU0ZscmFFOVdiVVYzVGxaQ1ZtSllhRXhhVmxwYVpWVTFWazlXWkU1U1JWcFlWa2Q0YjJReFpFaFNXR2hxVW10S1lWWnRlSGRVUm13MlUyczVVMDFyY0VoWGEyUnpWMFpLY21OR1dsZGlSbkJ5V2xkek1WWXhXblZVYkVwcFVteHdVRlpYZEZOUmF6bFNVRlF3UFE9PQ==
```
It appeared to be base64 encoded many times over, so after decoding it it read:
> Oh my goodness! It's gotten so bad. The cuils are rising... they want
> to outlaw encryption... I'd rate their world +200 Cuils! At least I have
> MegaEncryption (TM) to keep me safe. `the_cuil_is_too_much_to_handle`

### Authorization of Time
![qr](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/qr.png)
> `1489798809000`. Get me in.
>
> Big ben just hit `1`.

My teammate solved this one by reading the QR code with his Google Glasses
to get the secret text. I used a [website](https://zxing.org/w/decode.jspx)
to do the same.
```
otpauth://totp/PACTF%3AMiles?secret=ORUW2ZK7OBQXG43FONPWE5LUL5RXK2LMONPXG5DBPFPXI2DFL5ZWC3LF
```

He then used the secret value and `pyotp` to authenticate at the right time.
```python
>>> import time
>>> import pyotp
>>> secret = 'ORUW2ZK7OBQXG43FONPWE5LUL5RXK2LMONPXG5DBPFPXI2DFL5ZWC3LF'
>>> n = 1489798809000
>>> time.ctime(n//1000)
'Fri Mar 17 20:00:09 2017'
>>> totp = pyotp.TOTP(secret)
>>> totp.at(n//1000)
'808365'
```

There we have it, our flag is `808365`.

### ETA?
> If this script were to finish, what would it output?
> [MacOS](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/mac.out)
> [Linux](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/linux.out)
>
> Try doing a bit of dynamic binary analysis. Read the registers!

Looking at the binary, we determined it was calculating primes up
until `0x7fffff` and printing the last one, so the flag was `8388617`.

### Haystack
> That’s a lot of data… but at least it’s signed! I’ll bet that
> at least one signature doesn’t match up, though. One of ‘em has a
> bit of cuil. It’ll be like finding a needle in a haystack…
>
> [haystack.json](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/haystack.json)
>
> Yes, I know that the RSA key format doesn’t really matter in this
> scenario (considering there isn’t much of a format).

This one took awhile because I thought the actual data was base64 encoded,
so I was trying to verify after decoding, but eventually we used a python
script to verify each entry.
```python
from Cryptodome.PublicKey import RSA
from Cryptodome.Hash import SHA256
from Cryptodome.Signature import pkcs1_15
from base64 import b64decode

key = RSA.construct((n, 65537))
pub = key.publickey()
signer = pkcs1_15.new(pub)

for hay in haystack:
    digest = SHA256.new()
    digest.update(b64decode(hay['data']))
    try:
        signer.verify(digest, b64decode(hay['signature']))
    except:
        print(digest, hay['data'], hay['signature'])
```

The only one that didn't match was:
```
WW91IGZvdW5kIGl0ISBGbGFnOiBzb21ldGltZXNfbmVlZGxlc19pbl9hX2hheXN0YWNrX2Nhbl9wcmlja195b3U=
```

Decoding that:
> You found it! Flag: `sometimes_needles_in_a_haystack_can_prick_you`

Interestingly, we could have decoded each entry and grepped for something
we know is in the flag (like underscores).
```bash
for line in $(grep data haystack.json | awk '{print $2}' | awk -F\" '{print $2}'); do
    echo $line | base64 -d - | grep -I '[a-z]_[a-z]' && echo $line
done
```

### What 3 Words
> `preheated.positions.using` `gifted.miss.store`! `increases.enigma.sting` is
> `intellects.papers.downpours`. Please capitalize.
>
> Use the default map, not OSM.

Fortunately my teammate figured this one out because I had no idea what
it was asking. Apparently there is a website called
[What 3 Words](https://map.what3words.com) that maps three words to
a location.

![well](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/well.png)
![done](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/done.png)
!
![flag](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/flag.png)
is
![longyearbyen](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/longyearbyen.png)

Each location spelled out a message: "Well done! [The] flag is `Longyearbyen`."

### Visual Words
> Some of us are visual learners. Some of us learn best from texts. I’ve
> found the perfect combination! It was a bit dark though, so I had to
> make it brighter by some factor.
>
> Number, words, colors, data. It’s all really just numbers and
> math right?

![Visual Words](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/visual.png)

Using python, I found the first pixel color was `(204, 216, 194)`. I
noticed each pixel value was even, so dividing by 2 and converting to
a character revealed this pixel was really `'fla'`. It wasn't hard to
extrapolate and acquire the entire flag.
```python
>>> from PIL import Image
>>> img = Image.open('visual.png')
>>> pixels = list(img.getdata())
>>> ''.join(map(lambda y: ''.join(map(lambda x: chr(x//2), y)), pixels[:20]))
'flag_HidingSomeFunStuffInThisImage!!flag_HidingSomeFunStuffI'
```

### Bitesized
> There’s an image of some trees here. I bet the image contains more
> than trees, though.
>
> Try mod 8?

![trees](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/trees.png)

Following the hint's suggestion, I did mod 8 on each pixel (followed by
a multiplication by 8) and saved the output to an image.
![trees mod8](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/trees-mod8.png)

I could faintly make out some text, so I thresholded each pixel to make
it more clear, and there's the flag.
![trees secret](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/trees-secret.png)

Below is the code I used.
```python
from PIL import Image
# get pixels
img = Image.read("trees.png")
pixels = list(img.getdata())

# mod 8 each pixel
pixels = [tuple(map(lambda x: (x % 8)*8, pixel)) for pixel in pixels]

# save output
out = Image.new(img.mode, img.size)
out.putdata(pixels)
out.save("trees-mod8.png")

# threshold
pixels = [(255, 255, 255) if sum(pixel) > 100 else (0, 0, 0) for pixel in pixels]
out.putdata(pixels)
out.save("trees-secret.png")
```

### RSA256
> According to Wikipedia, RSA 256 can be factored on
> modest hardware in 35 minutes. Given the encoded public key
> `MDwwDQYJKoZIhvcNAQEBBQADKwAwKAIhAIl47p5SrV3uMTsUAbwE0E+j+QynAY/CVq/Gf8IAOQy7AgMBAAE=`,
> what is the similarly-encoded private key? The first 32 characters is
> the flag.
>
> And apparently YAFU can do it in 103 seconds!

I first used python to get `n`.
```python
>>> from Crypto.PublicKey import RSA
>>> import base64
>>> base64_key = "MDwwDQYJKoZIhvcNAQEBBQADKwAwKAIhAIl47p5SrV3uMTsUAbwE0E+j+QynAY/CVq/Gf8IAOQy7AgMBAAE="
>>> key = RSA.importKey(base64.b64decode(base64_key))
>>> key.n
62180528787689937819999198320875604188782593051291064364537897810852251765947
```

Using YAFU, we found `p = 277271726050281009396301232126405463677` and
`q = 224258454597761606450883324956139951511`. Now that we have `p` and
`q` we just need to find `d` (using Wikipedia).
```python
>>> # return (g, x, y) a*x + b*y = gcd(x, y)
>>> def egcd(a, b):
...     if a == 0:
...         return (b, 0, 1)
...     else:
...         g, x, y = egcd(b % a, a)
...         return (g, y - (b // a) * x, x)
...
>>> # x = mulinv(b) mod n, (x * b) % n == 1
>>> def mulinv(b, n):
...     g, x, _ = egcd(b, n)
...     if g == 1:
...         return x % n
...
>>> l = (p-1)*(q-1) // egcd(p-1, q-1)
>>> d = mulinv(l, key.n)
```

```python
>>> d
10810931767876665036391364653054260523419878402727343505431379711767076903333
>>> key = RSA.construct((key.n, key.e, d))
>>> print(key.exportKey("PEM").decode())
-----BEGIN RSA PRIVATE KEY-----
MIGpAgEAAiEAiXjunlKtXe4xOxQBvATQT6P5DKcBj8JWr8Z/wgA5DLsCAwEAAQIg
F+bFQhSo3Nx1hcqINKr3lqeXSWYHqnZgFThZ0CujmaUCEQDQmJeM3hK54QG1GdQs
pU59AhEAqLabd/WTyoooDc19dX2VlwIQeXiiqDGaDgxthhyoZedNsQIQOPeX3VSV
t7EYvzhgoXhrNwIQURb6TS79gHK6f+npSDFBPw==
-----END RSA PRIVATE KEY-----
```

So our key is the first 32 bytes `MIGpAgEAAiEAiXjunlKtXe4xOxQBvATQ`.

## Part 2 Challenges
Below is the challenge and hint followed by a description of how we
found the flag for each of the challenges in Part 2.

### Exploring the Binary!!!
> Binary 1
>
> Why doesn’t [this file](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/exploring.out) print!! Did I forget to put in the print
> statement when I compiled?
>
> Well running it does nothing, maybe try looking inside?

My first step in binary analysis is always to use `strings`.
```bash
$ strings exploring.out | grep -i flag
flag_1297831859
```

### Time Travel
> Our website links to many web pages. Once upon a time, one of them displayed the flag.
>
> PACTF did not exist before 2015.

Poking around I the website, I thought maybe the System Status might
contain something. It had many pages of history, so I used cURL to
quickly search it.

```bash
$ curl https://pactf.statuspage.io/history\?page={1,2,3,4,5,6,7,8} | grep -i flag
```

![Time Travel](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/time-travel.png)


### XOR 1
> My friend Miles sent me a secret message. He said he encoded it with an
> XOR cipher. Can you figure out what his message “KGZFK\qZFG]qA\qZFOZ”
> means?
>
> The key is only one digit long

Because we know the key is only one digit long, we can `XOR` each character
in the message with something that we know is probably in the key (`_`), and
look for possible keys in the output.  This works because `(_ XOR key)
XOR _ == key`.

We see in the output below that there's only one printable character
that comes out, so that is most likely the key.
```python
>>> s = 'KGZFK\qZFG]qA\qZFOZ'
>>> ''.join(map(lambda x: chr(x ^ ord('_')), s.encode()))
'\x14\x18\x05\x19\x14\x03.\x05\x19\x18\x02.\x1e\x03.\x05\x19\x10\x05'
>>> ''.join(map(lambda x: chr(x ^ ord('.')), s.encode()))
'either_this_or_that'
```

### Impossible Conditions
> Impossible Conditions
>
> Let’s make the impossible possible! Let’s make 1=5. [impossible.out](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/impossible.out)
>
> If you were to solve this, I would jmp for joy!

I used `snowman` to decompile the binary into a somewhat more readable
C style program.

![snowman - Impossible](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/impossible.png)

---

![snowman - Impossible Compare](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/impossible-compare.png)

After much inspection, it appears the code we want is in that `if`
statement. This code is taking the current time and comparing it to
"Nope". If they are equal (which is impossible), it will print out
the flag.

Looking at the code, it appeared that the flag might be `ndglizxcba`,
but it is actually printing them out backwards (and `z = 33` or `!`). I
figured this out by using `gdb` and making the compare work.

```
(gdb) b *0x4013d3
Breakpoint 1 at 0x4013d3
(gdb) r
Starting program: impossible.out

Breakpoint 1, 0x00000000004013d3 in main ()
(gdb) call strcpy((char*)($rbp-96), "Nope")
$1 = -8480
(gdb) c
Continuing.
abcx!ilgdn
[Inferior 1 (process 5581) exited normally]
```

### Zeroes and Ones
> Bit String Flicking
>
> How many solutions are there for X in the expression:
>
> LCIRC -3 (01011 AND X OR 10100) = 01101
>
> Try simplifying it?

I had not heard of LCIRC before this, but being an engineer I am very
familiar with bit manipulations.

```
    LCIRC -3 (01011 & X | 10100) = 01101
             (01011 & X | 10100) = 10101
             (01011 & X)         = 00001
    X = 00001, 00101, 10001, 10101
```

Therefore the flag is `4`.

### Open Sourcery 2
> There are many strings in the source code of the latest Google
> Chrome. Some of the strings contain `pactf`. Find the one that starts with
> `i`, ends with `e`, and also contains the letter `v`.
>
> Maybe you could get the source from the binary. But what’s this
> relationship between Chrome and Chromium I heard? Also, just so you know,
> this flag doesn’t have the string `flag` in or around it.

Chromium has a link on their web page to browse the source online, and
fortunately it uses regex for their search. To find a string starting with
`i`, ending with `e`, and containing `pactf` I used the regular expression
`\"i[^\"]*pactf[^\"]*e\"` which brought me to this page.

![Chromium Search Result](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/chromium.png)

The flag is `impactfestival.be`.

### PACTF DRM
> PACTF DRM
>
> The flag is in [encrypted.txt](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/encrypted.txt).
> You also get the [decoder software](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/decoder.out).
> Unfortunately, the software is protected by its own custom DRM (Digital
> Rights Management). …And it’s very protective about its things.
>
> You’ll need to somehow make it change its mind?

Running the program didn't do much. Looking at it with `snowman`, there
is a decrypt function, however it doesn't actually decrypt. I then looked
at the encrypt function to see it was taking each character and adding
it to itself then `XOR`ing with 46.

![Decoder Snowman](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/decoder.png)

It is trivial to reverse this operation.

```python
>>> fp = open('encrypted.txt', 'r')
>>> ''.join(map(lambda x: chr((int(x) ^ 46)//2), fp.read().split()))
'm3_LIKES_dat_DRM'
```

### Quantum Theory
> (S//REL) TURMOIL
>
>     or LPT, LPT-D, what else can you kludge for tipping… cough… [FLAG REDACTED] […]
>     (TS//SI//REL) QUANTUMINSERT
>
>     HTML Redirection
>
> My igloo is leaking!

This has something to do with Wikileaks and Snowden, but googling
"what else can you kludge" gave us the flag.

![Quantum Flag](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/quantum.png)

### Rembmer md5?
> Remember md5?
>
> Oh. Those good old days of md5 and huge rainbow tables. I get nostalgic
> just thinking about it. Well, you can’t use one of those to break my
> password. It still isn’t very secure though. I only used my favorite
> characters ‘a’, ‘b’, and ‘c’. I remember it being about 14
> characters long. The hash is 1b657b7fe26eda5b3c1309d340f1674d.
>
> When all else fails, try brute force.

Brute force is really the only option for this, so I wrote a python
script to do so. I used a Stack Overflow script for unique permutations
combined with python's functionality to create all iterations of a
certain length.

```python
import itertools as it
import hashlib
import sys

class unique_element:
    def __init__(self,value,occurrences):
        self.value = value
        self.occurrences = occurrences

def perm_unique(elements):
    eset=set(elements)
    listunique = [unique_element(i,elements.count(i)) for i in eset]
    u=len(elements)
    return perm_unique_helper(listunique,[0]*u,u-1)

def perm_unique_helper(listunique,result_list,d):
    if d < 0:
        yield tuple(result_list)
    else:
        for i in listunique:
            if i.occurrences > 0:
                result_list[d]=i.value
                i.occurrences-=1
                for g in  perm_unique_helper(listunique,result_list,d-1):
                    yield g
                i.occurrences+=1

if __name__ == '__main__':
    hash = '1b657b7fe26eda5b3c1309d340f1674d'
    guesses = [''.join(x) for x in it.combinations_with_replacement('abc', 15)]
    for guess in guesses:
        for g in perm_unique(guess):
            g = ''.join(g)
            h = hashlib.md5(g.encode()).hexdigest()
            if h == hash:
                print(g)
                sys.exit(0)

    sys.exit(1)
```

Annoyingly, the password was actually 15 characters long, not 14:
`abbabcbabcbabcb`.

### XOR 2
> Miles just sent me a really cool article to read! Unfortunately, he
> encrypted it before he sent it to me. Can you crack the code for me so
> I can read the article? [Article.txt](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/Article.txt)
>
> Did you know that in typical English writing, a character is the same as
> the one k characters in front of it about 8% of the time, regardless of k?

Because this is a very long article with a shorter key, this is very
easy to decode. If I were doing this from scratch I would have started
with finding the most likely key size based on the frequency (as the
hint points to), however I have the Internet on my side.
[Wiremask](https://wiremask.eu/tools/xor-cracker/) did all the work
for me. It found the key `frqncyislove` and decoded the entire
[article](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/article-decoded.txt),
which included a line `The flag is primes_are_cool`.

### Hash Killer
> Qu’est que c’est?
>
> We were clearing out the old server and came across a really weird
> [file](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/hashes.txt)…
>
> Did someone say MD5? And that last line seems different from the rest…

Using [crackstation](https://crackstation.net/), I decoded all of the
hashes except one. I was able to find the last one on a different rainbow
table website. Each hash was a word resulting in the message:

```
oh you wish it were this easy? sorry to let you down but this rabbit
hole goes deeper! the word 'AES' hash is the key now decrypt me START
```

So now we know we have to use AES to decrypt
`mJRKaaMSR1atUGs0kOkAJP3dty9tjCvXKMzWDHtZdRQ=`. It took some time to
figure out the right configuration, but it was AES in ECB mode with the
key being the hex digest of the md5 hash of 'AES'.

```python
>>> from Crypto.Cipher import AES
>>> import base64
>>> import hashlib
>>> cipher = base64.b64decode('mJRKaaMSR1atUGs0kOkAJP3dty9tjCvXKMzWDHtZdRQ=')
>>> aes = AES.new(hashlib.md5(b'AES').hexdigest(), AES.MODE_ECB)
>>> aes.decrypt(cipher)
b'flag{w3_l0v3_3ncrypt10n}\x00\x00\x00\x00\x00\x00\x00\x00'
```

### Synesthesia
> Yatharth just dropped his fire new mixtape, but the last track is somewhat
> of a mystery. It’s called ‘See the Sound,’ and no one really knows
> what to make of it.
>
> [see_the_sound.wav](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/see_the_sound.wav)
>
> Look at it!

To look at the waveform, I use Audacity. At first I thought it was in
binary or morse code, however that didn't reveal anything. It ended up
being spectrum that revealed the flag: `just_like_batman`.

![Synesthesia](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/synesthesia.png)

### Decider.
> Decider. [Tie-Breaker](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/decider.out) - all u get is that and [this](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/challenges/decider-cipher.txt)
>
> No help for this one.

This one took awhile to figure out. I used `snowman` again to decompile
it.

![Decider Snowman 1](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/decider-1.png)

---

![Decider Snowman 2](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/decider-2.png)

---

![Decider Snowman 3](https://raw.githubusercontent.com/mcastorina/ctfs/master/pactf-2017/img/decider-3.png)

At a high level, it is doing three computations before printing
`"Encrypted To:"`. Looking at the strings in the code, it appears to be
encrypting `"Good Luck!"`. I only figured out what the first two
computations were doing, and that was enough to decrypt the flag. The last
one only changed the output some of the time, which really threw me off.

Anyway, the first step is taking each character, converting it to an
integer, and summing it with the next character (e.g. the first number in
`Good Luck!` is `G` + `o` = 182). If it was the last character there
was no change to it.

It then converted each number to a string plus a comma afterwards
(e.g. `182,222,211,`...).

The second step took each character in the string above and `XOR`ed it
with 97. The output of this is the resulting string, so to get the flag
we just needed to reverse this very reversible process.

```python
def encode(message):
    message = list(map(ord, message))
    ords = []
    for i in range(len(message)-1):
        ords.append(message[i+1] + message[i])
    ords.append(message[-1])
    ords = ','.join(map(str, ords))+','
    cipher = [ord(x) ^ 97 for x in ords]
    return ''.join(map(chr, cipher))

def decode(cipher):
    cipher = [ord(x) ^ 97 for x in cipher]
    cipher = ''.join(map(chr, cipher))
    ords = cipher.split(',')[:-1]
    ords = [int(x) for x in ords]

    message = [ords[-1]]
    for i in ords[:-1][::-1]:
        message.insert(0, i - message[0])
    return ''.join(map(chr, message))

if __name__ == '__main__':
    print(decode(encode("Good Luck!")))
    print(decode("SPQMSQTMSQQMPXYMSPVMSSVMSQWMSQQMPWYMPVPMPWYMPRXMPRQMPWQMSQWMSSSMSQXMSPPMSRSMPYUMPYSMSPRMSSTMSRXMPPYM"))
```

```bash
$ python decider.py
Good Luck!
flag_ziecEfBI9ggwZyoImhyv
```

## Takeaways
I had fun and learned the following:

* The intended solution is not the only solution
* Have time for the problem to work itself out in the back of your mind
* Have a teammate to discuss ideas - it is hugely beneficial
* Use online tools as much as you can
* Use a decompiler for binary analysis or reverse engineering
