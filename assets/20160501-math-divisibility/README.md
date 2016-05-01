# Checking Divisibility

When you have a large number, there is a relatively quick and easy way to double check if it is divisible by the numbers: 2, 3, 4, 5, 6, 9 and 10.

2 - this is a matter of checking if the last digit is a multiple of 2, or 0. e.g. it is one of, 0, 2, 4, 6, 8.

3 - If you add all digits in the sequence, the end result just needs to be divisible (it'll be easier to recognise on a smaller number). If it's still large, you can repeat this process.

e.g. 576, 543 = 5+7+6+5+4+3  
= 30  
= 30 / 3   
(or 3+0 / 3)  
= 10 (or 1)

The theory behind this is that 576, 543 is the same as:

5(1 + 99 999) + 7(1+9 999) + 6(1+999) + 5(1+99) + 4(1 + 9) + 3

We separate 1 out from the number place in order to get a number divisibly by 3. With that, we get left with the unit of each place

5*1 + 7*1 + 6*1 + 5*1 + 4*1 + 3
= 5+7+6+5+4+3
= 30

note: this same concept applied for 9.
4 - The last two digits must be divisible by 4. We can use this rule because 100 is divisble by 4 (25), so we just need the final 2 to also be divisible by 4  

5 - Must end in either 5 or 0

6 - Must be divisible by both 2 and 3

9 - Refer to logic for rule 3 - the same rules apply

10 - Must end in 0
