### Sonic Pi Song From CSV File
Hello, my name is Joe. I recently bought a Raspberry Pi and discovered the Sonic PI application.
 I’m new to Sonic Pi and the Ruby language it is written in. 
So a lot of ideas are floating around my head that I’m itching to code. So here is just one of the ideas.
I want to write a song (perhaps from sheet music) using a spreadsheet, save it as a comma separated file (CSV) and the have Sonic Pi read the file and play the music
#### The song is contained in a CSV file
So here is the song Frere Jacques saved as freejaq1.csv

| tone| time |
|---:| ---:|
| c4 | 0.25 |
| d4 | 0.25 |
| e4 | 0.25 |
| c4 | 0.25 |
| c4 | 0.25 |
| d4 | 0.25 |
| e4 | 0.25 |
| c4 | 0.25 |
| e4 | 0.25 |
| f4 | 0.25 |
| g4 | 0.5 |
| e4 | 0.25 |
| f4 | 0.25 |
| g4 | 0.5 |
| g4 | 0.125 |
| a4 | 0.125 |
| g4 | 0.125 |
| f4 | 0.125 |
| e4 | 0.25 |
| c4 | 0.25 |
| g4 | 0.125 |
| a4 | 0.125 |
| g4 | 0.125 |
| f4 | 0.125 |
| e4 | 0.25 |
| c4 | 0.25 |
| g3 | 0.25 |
| c4 | 0.25 |
| c4 | 0.25 |
| g3 | 0.25 |
| c4 | 0.25 |

#### Reading the CSV file
```
# Ruby is the computer language that Sonic Pi uses
# load library in to Ruby to read CSV files
require 'csv'
# point to the song file
songfile="c:/home/songs/freejaq1.csv"
#read the csv file into an array of Ruby hashes
song = Array.new
CSV.foreach(songfile, { encoding: "UTF-8", headers: true, header_converters: :symbol, converters: :all}) do |row|
  song << row.to_hash
end
#puts song
```
#### Setup some counter variables, the tempo and select the synthesiser

```
# a variable to count music bars or measures
barcount=0
barcnt=0
# setup tempo variable
tempo=1.0 #normal
#tempo=0.5 #faster
#tempo=2.0 #slower
# select the Sonic Pi synthesiser to use as voice
use_synth :fm
```
#### Use a live Loop to Play the song

```
# set up a live loop to play the song
live_loop :bar1 do
  note=song.tick
  unless note==nil
    #puts note
    puts note[:tone].intern
    # add up note times
    barcnt=barcnt+note[:time]
    #test for a measure (barcount)
    if(barcnt==1)
      barcnt=0
      barcount += 1
      puts "barcount= ",barcount
    end
    play note[:tone].intern
    # the .intern converts a string to a symbol
    sleep note[:time]*tempo
  else #end of song seen
    tick_reset #causes it to play again
    sleep 0.1
  end
end
```
See PlaySongSyn1.txt
#### Adding additional sounds
See PlaySongSyn3Freejaq.txt
Use the barcount to trigger additional voices

```

# set up a 2nd live loop to play the song
live_loop :bar2 do
  sync :bar1
  if(barcount>1)
    puts look
    note=song.tick
    unless note==nil
      #puts note
      puts note[:tone].intern
      play note[:tone].intern
      # the .intern converts a string to a symbol
      sleep note[:time]*tempo
    else #end of song seen
      #tick_reset #causes it to play again
      sleep 0.1
    end
  end
end

# set up a 3ird live loop to play the song
live_loop :bar3 do
  sync :bar1
  if(barcount>3)
    puts look
    note=song.tick
    unless note==nil
      #puts note
      puts note[:tone].intern
      play note[:tone].intern
      # the .intern converts a string to a symbol
      sleep note[:time]*tempo
    else #end of song seen
      #tick_reset #causes it to play again
      sleep 0.1
    end
  end
end
```
##### Converting the song array to a ring for continous play
After you read the file into the array named song use the following
```
song=song.ring
```
#### Discussion
Would it be better to write the song so that the octave is separate from the note?
Suppose you have a C4 and in a second voice your want to drop it an octave to C3.
If the octave and note are separate items in the file and combined before playing the note you could subtract 1 from the octave.
Suppose you want to harmonize a note. If the octave and note are separate fields, then a function could be used to raise or lower the note by a given interval such as a third or a fifth.
This method could be used to read several files into different arrays for different parts or voices of a song.