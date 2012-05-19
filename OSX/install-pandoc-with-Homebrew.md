# Install pandoc with Homebrew

Since pandoc is written in Haskell, you cannot install it directly with Homebrew.  But there is an indirect way to install it using Homebrew.  Before you ask, pandoc does have an OSX installer, but I prefer to manage all of my tools with Homebrew.

### Assumptions

While this may work in earlier versions of OSX, I have only tested it on 10.7.4
Obviously you will also need a copy of Homebrew installed

### Step 1: Install Haskell Platform

Using Homebrew install the Haskell Platform.

    $brew install haskell-platform
    
This takes a few minutes so you will need to be patient.

### Step 2:  Install pandoc

In the previous step we installed the Haskell Platform which installs the cabal package manager.

    $cabal update
    $cabal install pandoc
    
By default, cabal installs pandoc in ~/.cabal/bin/pandoc.  This can be changed, but I just added this to my path and am done with it.
    
Now you are ready to go.
    