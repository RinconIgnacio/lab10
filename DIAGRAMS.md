# Lab 10 — Dynamic Memory Diagrams

## Shallow copy versus deep copy

Shallow copy (incorrect):

Original.cards_ ----+
                    +----> [Card 0][Card 1]...[Card 51]
Backup.cards_ ------+

Both objects point to the same array. Destroying one leaves the
other with a dangling pointer and can cause a double-free.

Deep copy (correct):

Original.cards_ --------> [Card 0][Card 1]...[Card 51]
Backup.cards_ ----------> [Card 0][Card 1]...[Card 51]

Each object owns a separate array with identical card values.

## Shuffle flowchart

Start
  |
  v
Set i = m_size - 1
  |
  v
Is i > 0? -- No --> Set m_next = 0 --> End
  |
 Yes
  v
Choose random j from 0 through i
  |
  v
Swap m_cards[i] and m_cards[j]
  |
  v
Decrease i by 1
  |
  +--------> Repeat condition

## Copy constructor flowchart

Start
  |
  v
Allocate a new array of other.m_size Cards
  |
  v
Copy m_size and m_next from other
  |
  v
Copy each Card into the new array
  |
  v
End: independent deep copy

## Assignment operator flowchart

Start
  |
  v
Is this == &other? -- Yes --> Return *this
  |
 No
  v
Allocate new array
  |
  v
Copy all cards from other
  |
  v
Delete old m_cards array
  |
  v
Assign new pointer, m_size, and m_next
  |
  v
Return *this
