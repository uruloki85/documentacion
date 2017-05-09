# Shellscripting
## Arrays
Length of an array:   
```bash
array_length="${#arr[@]}"
```
Iterate over an array:
```bash
for (( i=0; i<$array_length-1; i++ )); do 
   echo "${arr[$i]}"
done
```
or
```bash
for i in ${arr[@]}; do 
  echo $i; 
done
```
## Parallel
For running several commands in parallel.
```bash
curl_sample="curl -X POST -H \"X-Token: $token\" -H \"Content-type: application/json\" $SUBMITTER_URL/submissions/$submission_id/samples -d {}"
parallel_command="parallel -j10 --joblog parallel_log -a file.txt $curl_sample"
echo "parallel command: $parallel_command"
# Run the command discarding output
$parallel_command &> /dev/null
```
* <code>{}</code>: the input data will be inserted here.
* <code>--joblog</code>: the log will be written in this file.
* <code>-a</code>: input data is a file.
<b>IMPORTANT</b>: Notice that the file must contain several entries (ending with newline) in order to execute more than processes.E.g. file.txt contains 2 entries, 2 jobs will be executed.
