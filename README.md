Problem statement and solution overview:
The goal of this assignment was to use AI to help find global execution flags and then edit them. The first thing that needed to be changed was to make long-format listing the default behavior for ls.c and the second change was to add "you are: " to whoami.c. So I gave the AI the code and asked it to help me locate the areas that needed changed, cloned the repository, and then modified the code within the repository.

C system mechanics & Diff analysis:
For ls.c, I got rid of ls_mode == LS_LS ? (stdout_isatty () ? many_per_line : one_per_lin) and replaced it with ls_mode == LS_LS ? long_format. This set the default to display the file information whether or not the output is going to the terminal. For whoami.c, I changed puts (pw->pw_name); to printf ("You are: %s\n", pw->pw_name); which does the same thing as the original code but also prints "You are: " before the output.

kade1@KadensComputer:~/coreutils$ git --no-pager diff -- src/ls.c src/whoami.c
diff --git a/src/ls.c b/src/ls.c
index 1078f882f..339082cba 100644
--- a/src/ls.c
+++ b/src/ls.c
@@ -2312,8 +2312,7 @@ decode_switches (int argc, char **argv)
     }
 
   format = (0 <= format_opt ? format_opt
-            : ls_mode == LS_LS ? (stdout_isatty ()
-                                  ? many_per_line : one_per_line)
+            : ls_mode == LS_LS ? long_format
             : ls_mode == LS_MULTI_COL ? many_per_line
             : /* ls_mode == LS_LONG_FORMAT */ long_format);
 
diff --git a/src/whoami.c b/src/whoami.c
index 9313eaf3e..bc7e23b53 100644
--- a/src/whoami.c
+++ b/src/whoami.c
@@ -83,6 +83,6 @@ main (int argc, char **argv)
   if (!pw)
     error (EXIT_FAILURE, errno, _("cannot find name for user ID %ju"),
            (uintmax_t) uid);
-  puts (pw->pw_name);
+  printf ("You are: %s\n", pw->pw_name);
   return EXIT_SUCCESS;
}

Pros and cons of solution:
The main pros of my code solution is that it is very small and simple. It only changes a couple lines of code leaving the rest of it completely the same, which makes it far less likely for new errors to appear. Another pro is that both changes make things simpler and more clear, as the ls.c one will always provide detailed info and the whoami.c one is easier for the user to understand. As for cons, There could be errors in scripts that expect ls.c to be in short listing and similarly, if there are other scripts that depend on whoami.c only printing the name and not "you are: ", they could also no longer work.

AI audit reflection:
For the prompts I used to navigate the repository, I simply gave it both the ls.c code and the whoami.c code, and told it what what listed in Part 1 and Part 2 of the assignment, and asked it to help me find the locations so I could change the code. As for the insights, it explain how the code currently worked by explaining what ls_mode did and also how roles like geteuid() are used in getting the users name. Lastly, It also gave me a simple command to run to verify the changes which was ~/coreutils$ git --no-pager diff -- src/ls.c src/whoami.c to see the changes. src/ls and whoami to run the new code, and also gave me git diff --check which checks for whitespace.
