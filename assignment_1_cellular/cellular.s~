########################################################################
# COMP1521 20T2 --- assignment 1: a cellular automaton renderer
#
# Written by <<YOU>>, July 2020.


# Maximum and minimum values for the 3 parameters.

MIN_WORLD_SIZE	=    1
MAX_WORLD_SIZE	=  128
MIN_GENERATIONS	= -256
MAX_GENERATIONS	=  256
MIN_RULE	=    0
MAX_RULE	=  255

# Characters used to print alive/dead cells.

ALIVE_CHAR	= '#'
DEAD_CHAR	= '.'

# Maximum number of bytes needs to store all generations of cells.

MAX_CELLS_BYTES	= (MAX_GENERATIONS + 1) * MAX_WORLD_SIZE

	.data

# `cells' is used to store successive generations.  Each byte will be 1
# if the cell is alive in that generation, and 0 otherwise.

cells:	.space MAX_CELLS_BYTES


# Some strings you'll need to use:

prompt_world_size:	.asciiz "Enter world size: "
error_world_size:	.asciiz "Invalid world size\n"
prompt_rule:		.asciiz "Enter rule: "
error_rule:		.asciiz "Invalid rule\n"
prompt_n_generations:	.asciiz "Enter how many generations: "
error_n_generations:	.asciiz "Invalid number of generations\n"

	.text

	# $t0 = world_size
	# $t1 = rule 
	# $t2 = n_generations
	# $t3 = reverse
	# $t4 = cells
	# $t6 = MIN_WORLD_SIZE  (for checking valid world size)
	# $t7 = MAX_WORLD_SIZE  (for checking valid world size)
	# $t6 = MIN_RULE        (for checking valid rule)
	# $t7 = MAX_RULE        (for checking valid rule)
    # $t6 = MIN_GENERATIONS (for checking valid n_generations)
    # $t7 = MAX_GENERATIONS (for checking valid n_generations)
    # $t7 = (world_size / 2) (for calculating first generation alive cell)
    # $t6 = 1 (for inputting first generation alive cell into cells)
    # $t6 = g (value = 1 for run_generations for loop)
    # $a1 = world_size  (for run_generations loop and print_generation loop)
    # $a2 = g           (for run_generations loop and print_generation loop)
    # $a3 = rule        (for run_generations loop)
	
	#
	# YOU SHOULD ALSO NOTE WHICH REGISTERS DO NOT HAVE THEIR
	# ORIGINAL VALUE WHEN `run_generation' FINISHES	
    # $s0 
	# $s1
	# $s2 
	# $s3
	# $s4
	# $s5
	# $s6
	# $s7

main:
	#
	# REPLACE THIS COMMENT WITH YOUR CODE FOR `main'.
	#
	
	addi $sp, $sp, -4           # move stack pointer down by 4 bytes
	sw   $ra, 0($sp)            # save $ra on $sp (stack)  
	
	la   $a0, prompt_world_size # printf("Enter world size: ");
	li   $v0, 4
	syscall
	
	li   $t0, 0                 # int world_size = 0;
	li   $v0, 5                 # scanf("%d", &world_size);
	syscall 
    move $t0, $v0               # take scanned int and put in $t0 (world_size)
    
    # if (world_size < MIN_WORLD_SIZE || world_size > MAX_WORLD_SIZE) {
    #     printf("Invalid world size\n");
    #     return 1;
    # }
    li  $t6, MIN_WORLD_SIZE     # $t6 = MIN_WORLD_SIZE
    li  $t7, MAX_WORLD_SIZE     # $t7 = MAX_WORLD_SIZE
    bgt $t6, $t0, main_invalid_world_size   
    # branch if MIN_WORLD_SIZE > world_size
    bgt $t0, $t7, main_invalid_world_size
    # branch if world_size > MAX_WORLD_SIZE
    j   main_valid_world_size     
    # jump past main_invalid_world_size if world_size is within range
    
main_invalid_world_size:
    la $a0, error_world_size    # printf("Invalid world size\n");
    li $v0, 4
    syscall
    
    j  main_return_1            # return 1;

main_valid_world_size:  

    la   $a0, prompt_rule       # printf("Enter rule: ");
    li   $v0, 4
    syscall     
    
    li   $t1, 0                 # int rule = 0;
    li   $v0, 5                 # scanf("%d", &rule);
    syscall
    move $t1, $v0               # take scanned int and put in $t1 (rule)
    
    # if (rule < MIN_RULE || rule > MAX_RULE) {
    #     printf("Invalid rule\n");
    #     return 1;
    # }
    li  $t6, MIN_RULE                       # $t6 = MIN_RULE
    li  $t7, MAX_RULE                       # $t7 = MAX_RULE
    bgt $t6, $t1, main_invalid_rule         # branch if MIN_RULE > rule
    bgt $t1, $t7, main_invalid_rule         # branch if rule > MAX_RULE  
    j main_valid_rule 
    # jump past main_invalid_rule if rule is within range      
    
main_invalid_rule:
    la $a0, error_rule          # printf("Invalid rule\n");
    li $v0, 4
    syscall
    
    j  main_return_1            # return 1;

main_valid_rule:
    
    la $a0, prompt_n_generations    # printf("Enter how many generations: ");
    li $v0, 4
    syscall
    
    li   $t2, 0             # int n_generations = 0;
    li   $v0, 5             # scanf("%d", &n_generations);
    syscall
    move $t2, $v0           # take scanned int and put in $t2 (n_generations)
    
    # if (n_generations < MIN_GENERATIONS || n_generations > MAX_GENERATIONS){ 
    #     printf("Invalid number of generations\n");
    #     return 1;
    # }
    li  $t6, MIN_GENERATIONS        # $t6 = MIN_GENERATIONS
    li  $t7, MAX_GENERATIONS        # $t7 = MAX_GENERATIONS
    bgt $t6, $t2, main_invalid_n_generations
    # branch if MIN_GENERATIONS > n_generations
    bgt $t2, $t7, main_invalid_n_generations
    # branch if n_generations > MAX_GENERATIONS
    j   main_valid_n_generations
    # jump past main_invalid_n_generations if n_generations is within range

main_invalid_n_generations:
    la $a0, error_n_generations     # printf("Invalid number of generations\n");
    li $v0, 4
    syscall
    
    j main_return_1                 # return 1;
    
main_valid_n_generations:
    la $a0, '\n'                    # putchar('\n');
    li $v0, 11
    syscall
    
    # negative generations means show the generations in reverse
    li  $t3, 0                      # int reverse = 0;
    bge $t2, $t3, main_normal_n_generations     
    # branch if n_generations >= 0 
    
main_reversed_n_generations:    
    li  $t3, 1              # reverse = 1;
    mul $t2, $t2, -1        # n_generations = -n_generations         

main_normal_n_generations: 
    # the first generation always has a only single cell which is alive
    # this cell is in the middle of the world
    li  $t6, 2
    div $t7, $t0, $t6       # $t7 = $t0 (world_size) / $t6 (2)
    la  $t4, cells          # $t4 = cells
    add $t4, $t4, $t7       # calculate cells[0][world_size / 2]
    li  $t6, 1              # $t6 = 1
    sb  $t6, ($t4)          # save 1 into $t4 (cells[0][world_size / 2])
    
    li $t6, 1               # $t6 = g = 1
    
main_run_generation_loop_start:
    bgt $t6, $t2, main_run_generation_loop_end  
    # exit loop if $t6 (g) > $t2 (n_generations)
    
    move $a1, $t0           # $a1 = $t0 (world_size)
    move $a2, $t6           # $a2 = $t6 (g)
    move $a3, $t1           # $a3 = $t1 (rule)
    jal  run_generation     # run_generation(world_size, g, rule);
    
    addi $t6, $t6, 1        # g++;
    j    main_run_generation_loop_start
    
main_run_generation_loop_end:
    lw   $ra, 0($sp)        # recover $ra from $sp (stack)
    add  $sp, $sp, 4        
    # move stack pointer back up to what it was when main called
    
    addi $sp, $sp, -4           # move stack pointer down by 4 bytes
	sw   $ra, 0($sp)            # save $ra on $sp (stack)  
	
    beqz $t3, main_normal_print_generation  # branch if $t3 (reverse) == 0

main_reversed_print_generation:
    move $t6, $t2               # int g = n_generations;
       
main_reversed_print_generation_loop_start:
    bltz $t6, main_return_0     # end loop if $t6 (g) < 0
    
    move $a1, $t0               # $a1 = $t0 (world_size)
    move $a2, $t6               # $a2 = $t6 (g)
    jal  print_generation
    
    addi $t6, $t6, -1            # g--;
    j    main_reversed_print_generation_loop_start

main_normal_print_generation:
    li $t6, 0                   # int g = 0;
    
main_normal_print_generation_loop_start:
    bgt $t6, $t2, main_return_0 # end loop if $t6 (g) > $t2 (n_generations)  
    
    move $a1, $t0               # $a1 = $t0 (world_size)
    move $a2, $t6               # $a2 = $t6 (g)
    jal  print_generation
    
    addi $t6, $t6, 1            # g++;
    j    main_normal_print_generation_loop_start
    
    
	# replace the syscall below with
	#
	# li	$v0, 0
	# jr	$ra
	#
	# if your code for `main' preserves $ra by saving it on the
	# stack, and restoring it after calling `print_world' and
	# `run_generation'.  [ there are style marks for this ]

	
main_return_0:
    lw   $ra, 0($sp)        # recover $ra from $sp (stack)
    add  $sp, $sp, 4        
    # move stack pointer back up to what it was when main called
    
	li $v0, 0
	j  main_end    
    
main_return_1:
    lw   $ra, 0($sp)        # recover $ra from $sp (stack)
    add  $sp, $sp, 4        
    # move stack pointer back up to what it was when main called
    
    li   $v0, 1
    
main_end:
    jr   $ra 

	#
	# Given `world_size', `which_generation', and `rule', calculate
	# a new generation according to `rule' and store it in `cells'.
	#

    # $s0 = x
	# $s1 = left
	# $s2 = centre
	# $s3 = right
	# $s7 = 0 (for checking if x > 0) and (world_size - 1)
	# $s6 = cells 
	# $s5 = cell position 
	# $s4 = x - 1
	# $s4 = bit
	# $s5 = set 
	# $s6 = 1 (to form bit)
    # $s7 = state 
    
	# YOU SHOULD ALSO NOTE WHICH REGISTERS DO NOT HAVE THEIR
	# ORIGINAL VALUE WHEN `run_generation' FINISHES
	# $s0 
	# $s1
	# $s2 
	# $s3
	# $s4
	# $s5
	# $s6
	# $s7

run_generation:
    li   $s0, 0         # int x = 0;
run_generation_loop_start:
    bge  $s0, $a1, run_generation_loop_end
    # exit loop if $s0 (x) >= $a1 (world_size)
    
    li   $s1, 0                             # int left = 0;
    li   $s7, 0                             # int checker = 0;
    bge  $s7, $s0, run_generation_left_end  # branches if $s7 (0) >= $s0 (x)
    
    la   $s6, cells         # $s6 = cells
    addi $s5, $a2, -1       # $s5 = which_generation - 1
    mul  $s5, $a1, $s5      # $s5 = world_size * (which_generation - 1)
    addi $s4, $s0, -1       # $s4 = x - 1
    add  $s5, $s5, $s4      # $s5 = $s5 + $s4 (x - 1)
    add  $s5, $s6, $s5      # $s5 = cell position
    lb   $s1, ($s5)         # $s1 (left) = cells[which_generation - 1][x - 1];
    
run_generation_left_end:
    la   $s6, cells         # $s6 = cells
    addi $s5, $a2, -1       # $s5 = which_generation - 1
    mul  $s5, $a1, $s5      # $s5 = world_size * (which_generation - 1)
    move $s4, $s0           # $s4 = $s0 (x)
    add  $s5, $s5, $s4      # $s5 = $s5 + $s4 (x)
    add  $s5, $s6, $s5      # $s5 = cell position
    lb   $s2, ($s5)         # $s2 (centre) = cells[which_generation - 1][x - 1];
    
    li   $s3, 0                             # int right = 0;
    addi $s7, $a1, -1                       # $s7 = $a1 (world_size) - 1
    bge  $s0, $s7, run_generation_right_end # branches if $s0 (x) >= $s7
    
    la   $s6, cells         # $s6 = cells
    addi $s5, $a2, -1       # $s5 = which_generation - 1
    mul  $s5, $a1, $s5      # $s5 = world_size * (which_generation - 1)
    addi $s4, $s0, 1        # $s4 = x + 1
    add  $s5, $s5, $s4      # $s5 = $s5 + $s4 (x + 1)
    add  $s5, $s6, $s5      # $s5 = cell position 
    lb   $s3, ($s5)         # $s3 (right) = cells[which_generation - 1][x + 1];
    
run_generation_right_end:
    # Convert the left, centre, and right states into one value.
    # int state = left << 2 | centre << 1 | right << 0;
    sll $s1, $s1, 2         # left = left << 2;
    sll $s2, $s2, 1         # centre = centre << 1;
    or  $s7, $s1, $s2       # $s7 (state) = $s1 (left << 2) | $s2 (centre << 1)
    or  $s7, $s7, $s3       # $s7 (state) = $s7 | $s3 (right)
    
    # And check whether that bit is set or not in the rule.
    # by testing the corresponding bit of the rule number.
    li   $s6, 1             # $s6 = 1
    sllv $s4, $s6, $s7      # $s4 (bit) = $s6 (1) << $s7 (state);
    and  $s5, $a3, $s4      # $s5 (set) = $a3 (rule) & $s4 (bit);
    
    la   $s6, cells             # $s6 = cells
    move $s4, $a2               # $s4 = $a2 (which_generation)    
    mul  $s4, $a1, $s4          # $s4 = world_size * (which_generation)
    add  $s4, $s4, $s0          # $s4 = $s4 + $s0 (x)
    add  $s4, $s4, $s6          # $s4 = cell position
      
    beqz $s5, run_generation_set_0  # branch if $s5 (set) = 0
    li $s7, 1           # $s7 = 1
    sb $s7, ($s4)       # store $s7 (1) $s4 (cells[which_generation][x])  
    
    j  run_generation_loop_middle
    
run_generation_set_0:
    li $s7, 0           # $s7 = 0
    sb $s7, ($s4)       # store $s7 (0) $s4 (cells[which_generation][x])
    
run_generation_loop_middle:
    addi $s0, $s0, 1    # x++;
    j    run_generation_loop_start

run_generation_loop_end:
	jr	$ra


	#
	# Given `world_size', and `which_generation', print out the
	# specified generation.
	#

    # $a1 = world_size
    # $a2 = which_generation
    # $s0 = x
    # $s1 = cells
    # $s2 = offset
    # $s3 = cell position
    # $s4 = byte value at $s3
	

	# YOU SHOULD ALSO NOTE WHICH REGISTERS DO NOT HAVE THEIR
	# ORIGINAL VALUE WHEN `print_generation' FINISHES
	# $v0 
	# $a0
	# $s0
	# $s1
	# $s2
	# $s3
	# $s4

print_generation:

	#
	# REPLACE THIS COMMENT WITH YOUR CODE FOR `print_generation'.
	#
    
    move $a0, $a2       # printf("%d", which_generation);   
    li   $v0, 1       
    syscall         
    
    la $a0, '\t'        # putchar('\t')
    li $v0, 11      
    syscall
    
    li $s0, 0           # $s0 = x = 0
print_generation_loop_start:
    bge  $s0, $a1, print_generation_loop_end     
    # end loop if $s0 (x) >= $a1 (world_size)
    
    la   $s1, cells     # $s1 = cells
    mul  $s2, $a1, $a2  # $s2 = $a1 (world_size) * $a2 (which_generation)
    add  $s2, $s2, $s0  # $s2 = $s2 + $s0 (x)
    add  $s3, $s1, $s2  # $s3 = $s1 (cells) + $s2 (offset)
    lb   $s4, ($s3)     # $s4 = byte value at $s3 
    
    beqz $s4, print_generation_dead_char
    
print_generation_alive_char:
    la $a0, ALIVE_CHAR  # putchar(ALIVE_CHAR);
    li $v0, 11
    syscall
    j  print_generation_loop_middle 
    
print_generation_dead_char: 
    la $a0, DEAD_CHAR   # putchar(DEAD_CHAR);
    li $v0, 11
    syscall   

print_generation_loop_middle:    
     
    addi $s0, $s0, 1    # x++;
    j    print_generation_loop_start    
        
print_generation_loop_end:    
    li $a0, '\n'        # putchar('\n');
    li $v0, 11
    syscall    
    
	jr	$ra
