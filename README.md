# Python-to-Exe
Convert Python to scripts to Exe


Finally made it!

Prepare your code in case you are loading you files like this:

    import os
    folder_path = os.path.join(path.dirname(__file__), 'folder')
    some_image = pygame.image.load(os.path.join(folder_path, 'some_image.png'))
Do this instead:

    import sys
    import os
## If the code is frozen, use this path:
    if getattr(sys, 'frozen', False):
        CurrentPath = sys._MEIPASS
## If it's not use the path we're on now
    else:
        CurrentPath = os.path.dirname(__file__)
## Look for the 'sprites' folder on the path I just gave you:
    spriteFolderPath = os.path.join(CurrentPath, 'sprites')
## From the folder you just opened, load the image file 'some_image.png'
    some_image = pygame.image.load(path.join(spriteFolderPath, 'some_image.png'))
It's needed because when you freeze your code, the files are gonna be moved to a different folder than the one you used before. Make sure you do this to all the files.

Here's another example:

    if hasattr(sys, '_MEIPASS'):  # the same logic used to set the image directory
    font = path.join(sys._MEIPASS, 'some_font.otf')  # specially useful to make a singlefile .exe
        font = pygame.font.Font(font, size)
## Don't ask me the difference between hasattr and getattr because I don't know. But it works.
Icon (optional)
If you want an icon that's not pyinstaller's default, you can choose a png image and convert it to .ico using some online converter (Google it). After that, put the .ico file on the same folder where your .py file is.

Make the spec file
At this point you should know if you want a single self contained .exe file or a bunch of separate files that you will zip and send to people. In any case, open the terminal on the folder where your .py file is.

If you want a single file, use this:

    pyinstaller --onefile --icon=icon_file.ico game_file.py
If you don't, use this:

    pyinstaller --icon=icon_file.ico game_file.py
If you don't want to set the icon now, just don't use the --icon part. You can change it later. What you can't change later is the --onefile option (at least as far as I know).

A file named game_file.spec will be created. It will automatically get the name from game_file.py. You can mess it up if they have different names, so don't get creative now. If you chose a single file, it should look like this:

    # -*- mode: python -*-

    block_cipher = None

    a = Analysis(['game_file.py'],
             pathex=['C:\\some\\path\\The path where your .py and .spec are'],
             binaries=None,
             datas=None,
             hiddenimports=[],
             hookspath=[],
             runtime_hooks=[],
             excludes=[],
             win_no_prefer_redirects=False,
             win_private_assemblies=False,
             cipher=block_cipher)
    pyz = PYZ(a.pure, a.zipped_data,
             cipher=block_cipher)
    exe = EXE(pyz,
          a.scripts,
          exclude_binaries=True,
          name='game_file',
          debug=False,
          strip=False,
          upx=True,
          console=True , icon='icon_file.ico')
If you chose to have a bunch of files, you'll see this aditional part:

    coll = COLLECT(exe,
               a.binaries,
               a.zipfiles,
               a.datas,
               strip=False,
               upx=True,
               name='game_file')
After block_cipher = None you're gonna add the files your game loads. Like this:

    added_files = [
         ( 'a folder', 'b folder' ),  # Loads the 'a folder' folder (left) and creates
                                      # an equivalent folder called 'b folder' (right)
                                      # on the destination path
         ( 'level_1/level2', 'level_2' ),  # Loads the 'level_2' folder
                                           # that's inside the 'level_1' folder
                                           # and outputs it on the root folder
         ( 'comic_sans.ttf', '.'),  # Loads the 'comic_sans.ttf' file from
                                    # your root folder and outputs it with
                                    # the same name on the same place.
         ( 'folder/*.mp3', '.')  # Loads all the .mp3 files from 'folder'.
         ]
Now you have to add 'added_files' here:

    a = Analysis(['game_file.py'],
                 pathex=['C:\\some\\path\\The path where your .py and .spec are'],
                 binaries=None,
                 datas=added_files,  # Change 'None' to 'added_files' here
                                     # Leave everything else the way it is.
                 hiddenimports=[],
                 hookspath=[],
                 runtime_hooks=[],
                 excludes=[],
                 win_no_prefer_redirects=False,
                 win_private_assemblies=False,
                 cipher=block_cipher)
You can also change some settings:

    exe = EXE(pyz,
              a.scripts,
              exclude_binaries=True,
              name='game_file',  # Name of the output file. Equivalent to '--name'
                                 # Don't change it.
              debug=False,  # If True shows a debug screen on start. Equivalent to '--debug'.
              strip=False,
              upx=True,  # Compresses executable files and libraries
              # If console=True, a console screen will be shown on start up.
              # icon= is the location of the icon of the exe.
              console=True , icon='icon_file.ico')
If you didn't change the path or the output name of the exe like I told you not to on the comments, we just need to run it and the .exe file that was previously created will be updated. Enter this on the command window:

    pyinstaller game_file.spec
Remember that game_file.spec is the file we just edited and 'game_file' is a random name I used as an example. Also notice that there's no --some_option because they won't work with the spec file. This is why you have to change them directly in the script. --onefile also doesn't work here and can't be done from within the script, that's why I told you to do it before.

You will se that two folders were created on the same folder where you .spec file is. The one called 'Dist' contains the exe file and if you didn't use --onefile, it should also have a bunch of other files that you need to zip along with the exe to share the application with other people. There will also be a 'Buid' folder but I don't know what it's for, since you don't need it to use the application.

So this is it. It should work for you.

My mistakes when I made the question was that I didn't know the sys._MEIPASS part (thanks again C._), the name of my spec file was different from my py file, I used '/sprites' instead of 'sprites' in added_files and didn't know I was supposed to run the spec file instead of the py file.

