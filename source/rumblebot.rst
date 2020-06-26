Develop robots locally with rumblebot
=====================================

Are you in love with your 10GB-large IDE? Are you a vimaholic that is annoyed by the millisecond delay caused by entering code in your browser? Rumblebot is here for you. `Download it here.`__

__ https://github.com/robot-rumble/cli/releases

Rumblebot serves two main functions:
 1. It allows you to easily run robots that exist in your filesystem, which makes it possible to use custom IDEs.
 2. It gives you the ability to skip the web interface by displaying battle results directly in your terminal.

Additionally, it also includes convenience commands for creating and updating your robots.

**Please note**: Just because your robots live in the filesystem does not mean that you can import external dependencies. Support for ``pip``/``npm`` is on roadmap, but implementing it is a very nuanced challenge. If you really, really need a library, your best bet is to minify it and just include it at the top of your program.
