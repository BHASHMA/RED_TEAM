
input_file = "rockyou.txt"
output_file = "rockyou_custom.txt"

min_len = 7
max_len = 12

seen = set()
count_in = 0
count_out = 0

with open(input_file, "r", encoding="latin-1", errors="ignore") as f_in, \
     open(output_file, "w", encoding="latin-1") as f_out:
    
    for line in f_in:
        pwd = line.rstrip("\r\n")
        count_in += 1
        if min_len <= len(pwd) <= max_len:
            if pwd not in seen:
                seen.add(pwd)
                f_out.write(pwd + "\n")
                count_out += 1

print(f"Input lines:  {count_in}")
print(f"Written lines (unique, length {min_len}-{max_len}): {count_out}")
