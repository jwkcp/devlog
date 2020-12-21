---
layout: post
title: 지킬(Jekyll)에서 코드 highlight 언어 지원 목록
tags: tools
comments: true
---

블로깅 툴로 Jekyll을 쓰시는 분들은 코드 표시를 위해 `highlight` 구문을 자주 쓰게 됩니다. `highlight` 구문은 아래의 언어를 지원합니다. 예전 Jekyll에서 [Pygment](https://pygments.org/languages/)를 사용했었는데 4버전 부터는 [Rouge](http://rouge.jneen.net/)로 바뀌었습니다. 기존에 `Pygment`를 쓰도록 설정에 되어 있는 분들은 자동으로 `Rouge`를 사용하도록 동작하게 됩니다. 이 둘은 100% 호환되니 너무 걱정하지 마세요. [여기](https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers)에서 이 목록의 최신본을 확인할 수 있고, [여기]를 방문하면 [지킬 공식페이지의 관련 설명](https://jekyllrb.com/docs/liquid/tags/)을 보실 수 있습니다.  

---

abap: SAP - Advanced Business Application Programming
actionscript: ActionScript [aliases: as,as3]
apache: configuration files for Apache web server
apiblueprint: Markdown based API description language. [aliases: apiblueprint,apib]
applescript: The AppleScript scripting language by Apple Inc. (http://developer.apple.com/applescript/) [aliases: applescript]
awk: pattern-directed scanning and processing language
biml: BIML, Business Intelligence Markup Language
brainfuck: The Brainfuck programming language
bsl: The 1C:Enterprise programming language
c: The C programming language
ceylon: Say more, more clearly.
cfscript: CFScript, the CFML scripting language [aliases: cfc]
clojure: The Clojure programming language (clojure.org) [aliases: clj,cljs]
cmake: The cross-platform, open-source build system
coffeescript: The Coffeescript programming language (coffeescript.org) [aliases: coffee,coffee-script]
common_lisp: The Common Lisp variant of Lisp (common-lisp.net) [aliases: cl,common-lisp,elisp,emacs-lisp]
conf: A generic lexer for configuration files [aliases: config,configuration]
console: A generic lexer for shell sessions. Accepts ?lang and ?output lexer options, a ?prompt option, and ?comments to enable # comments. [aliases: terminal,shell_session,shell-session]
coq: Coq (coq.inria.fr)
cpp: The C++ programming language [aliases: c++]
crystal: Crystal The Programming Language (crystal-lang.org) [aliases: cr]
csharp: a multi-paradigm language targeting .NET [aliases: c#,cs]
css: Cascading Style Sheets, used to style web pages
d: The D programming language(dlang.org) [aliases: dlang]
dart: The Dart programming language (dartlang.com)
diff: Lexes unified diffs or patches [aliases: patch,udiff]
digdag: A simple, open source, multi-cloud workflow engine (https://www.digdag.io/)
docker: Dockerfile syntax [aliases: dockerfile]
dot: graph description language
eiffel: Eiffel programming language
elixir: Elixir language (elixir-lang.org) [aliases: elixir,exs]
elm: The Elm programming language (http://elm-lang.org/)
erb: Embedded ruby template files [aliases: eruby,rhtml]
erlang: The Erlang programming language (erlang.org) [aliases: erl]
escape: A generic lexer for including escaped content - see Formatter.enable_escape! [aliases: esc]
factor: Factor, the practical stack language (factorcode.org)
fortran: Fortran 2008 (free-form)
fsharp: F# (fsharp.net)
gherkin: A business-readable spec DSL ( github.com/cucumber/cucumber/wiki/Gherkin ) [aliases: cucumber,behat]
glsl: The GLSL shader language
go: The Go programming language (http://golang.org) [aliases: go,golang]
gradle: A powerful build system for the JVM
graphql: GraphQL
groovy: The Groovy programming language (http://www.groovy-lang.org/)
hack: The Hack programming language (hacklang.org) [aliases: hack,hh]
haml: The Haml templating system for Ruby (haml.info) [aliases: HAML]
handlebars: the Handlebars and Mustache templating languages [aliases: hbs,mustache]
haskell: The Haskell programming language (haskell.org) [aliases: hs]
hcl: Hashicorp Configuration Language, used by Terraform and other Hashicorp tools
html: HTML, the markup language of the web
http: http requests and responses
hylang: The HyLang programming language (hylang.org) [aliases: hy]
idlang: Interactive Data Language
igorpro: WaveMetrics Igor Pro
ini: the INI configuration format
io: The IO programming language (http://iolanguage.com)
irb: Shell sessions in IRB or Pry [aliases: pry]
java: The Java programming language (java.com)
javascript: JavaScript, the browser scripting language [aliases: js]
jinja: Django/Jinja template engine (jinja.pocoo.org) [aliases: django]
json: JavaScript Object Notation (json.org)
json-doc: JavaScript Object Notation with extenstions for documentation
jsonnet: An elegant, formally-specified config language for JSON
jsp: JSP
jsx: React JSX (https://facebook.github.io/react/) [aliases: jsx,react]
julia: The Julia programming language [aliases: jl]
kotlin: Kotlin Programming Language (http://kotlinlang.org)
lasso: The Lasso programming language (lassosoft.com) [aliases: lassoscript]
liquid: Liquid is a templating engine for Ruby (liquidmarkup.org)
literate_coffeescript: Literate coffeescript [aliases: litcoffee]
literate_haskell: Literate haskell [aliases: lithaskell,lhaskell,lhs]
llvm: The LLVM Compiler Infrastructure (http://llvm.org/)
lua: Lua (http://www.lua.org)
m68k: Motorola 68k Assembler
magik: Smallworld Magik
make: Makefile syntax [aliases: makefile,mf,gnumake,bsdmake]
markdown: Markdown, a light-weight markup language for authors [aliases: md,mkd]
mathematica: Wolfram Mathematica, the world's definitive system for modern technical computing. [aliases: wl]
matlab: Matlab [aliases: m]
moonscript: Moonscript (http://www.moonscript.org) [aliases: moon]
mosel: An optimization language used by Fico's Xpress.
mxml: MXML
nasm: Netwide Assembler
nginx: configuration files for the nginx web server (nginx.org)
nim: The Nim programming language (http://nim-lang.org/) [aliases: nimrod]
nix: The Nix expression language (https://nixos.org/nix/manual/#ch-expression-language) [aliases: nixos]
objective_c: an extension of C commonly used to write Apple software [aliases: objc,obj-c,obj_c,objectivec]
ocaml: Objective Caml (ocaml.org)
pascal: a procedural programming language commonly used as a teaching language.
perl: The Perl scripting language (perl.org) [aliases: pl]
php: The PHP scripting language (php.net) [aliases: php,php3,php4,php5]
plaintext: A boring lexer that doesn't highlight anything [aliases: text]
plist: plist [aliases: plist]
powershell: powershell [aliases: posh,microsoftshell,msshell]
praat: The Praat scripting language (praat.org)
prolog: The Prolog programming language (http://en.wikipedia.org/wiki/Prolog) [aliases: prolog]
prometheus: prometheus [aliases: prometheus]
properties: .properties config files for Java
protobuf: Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data [aliases: proto]
puppet: The Puppet configuration management language (puppetlabs.org) [aliases: pp]
python: The Python programming language (python.org) [aliases: py]
q: The Q programming language (kx.com) [aliases: kdb+]
qml: QML, a UI markup language [aliases: qml]
r: The R statistics language (r-project.org) [aliases: r,R,s,S]
racket: Racket is a Lisp descended from Scheme (racket-lang.org)
ruby: The Ruby programming language (ruby-lang.org) [aliases: rb]
rust: The Rust programming language (rust-lang.org) [aliases: rs,rust,no_run,rs,no_run,rust,ignore,rs,ignore,rust,should_panic,rs,should_panic]
sass: The Sass stylesheet language language (sass-lang.com)
scala: The Scala programming language (scala-lang.org) [aliases: scala]
scheme: The Scheme variant of Lisp
scss: SCSS stylesheets (sass-lang.com)
sed: sed, the ultimate stream editor
shell: Various shell languages, including sh and bash [aliases: bash,zsh,ksh,sh]
sieve: mail filtering language
slim: The Slim template language
smalltalk: The Smalltalk programming language [aliases: st,squeak]
smarty: Smarty Template Engine [aliases: smarty]
sml: Standard ML [aliases: ml]
sqf: Status Quo Function, a Real Virtuality engine scripting language
sql: Structured Query Language, for relational databases
supercollider: A cross-platform interpreted programming language for sound synthesis, algorithmic composition, and realtime performance
swift: Multi paradigm, compiled programming language developed by Apple for iOS and OS X development. (developer.apple.com/swift)
tap: Test Anything Protocol [aliases: tap]
tcl: The Tool Command Language (tcl.tk)
terraform: Terraform HCL Interpolations [aliases: tf]
tex: The TeX typesetting system [aliases: TeX,LaTeX,latex]
toml: the TOML configuration format (https://github.com/mojombo/toml)
tsx: tsx
tulip: the tulip programming language (twitter.com/tuliplang) [aliases: tulip]
turtle: Terse RDF Triple Language, TriG
twig: Twig template engine (twig.sensiolabs.org)
typescript: TypeScript, a superset of JavaScript [aliases: ts]
vala: A programming language similar to csharp.
vb: Visual Basic [aliases: visualbasic]
verilog: The System Verilog hardware description language
vhdl: Very High Speed Integrated Circuit Hardware Description Language
viml: VimL, the scripting language for the Vim editor (vim.org) [aliases: vim,vimscript,ex]
vue: Vue.js single-file components [aliases: vuejs]
wollok: Wollok lang
xml: XML
xojo: Xojo [aliases: realbasic]
yaml: Yaml Ain't Markup Language (yaml.org) [aliases: yml]