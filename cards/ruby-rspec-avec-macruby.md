# RSpec avec MacRuby

## Installer MacRSpec

```
$ sudo macgem install --bindir /usr/local/bin --format-executable rspec -v '>=2.8'
```

Installe une version 2.8 ou plus récente de RSpec[^1], en plaçant l'exécutable RSpec avec un préfixe `mac`[^2] dans le dossier `/usr/local/bin`[^3].

## Pour exécuter les tests

```
$ cd project-dir/
$ macrspec --color .
.........................

Finished in 0.73303 seconds
25 examples, 0 failures
```
    
Notes
------

[^1]: compatible avec MacRuby 0.10 et 0.12
[^2]: semblable à ceux des exécutables MacRuby
[^3]: où se trouvent les exécutables MacRuby


(initially published on my [Makandra Cards › Ruby › RSpec avec MacRuby](https://makandracards.com/ruby/7345-rspec-avec-macruby))
