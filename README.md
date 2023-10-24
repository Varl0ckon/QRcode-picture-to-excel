# QRcode-picture-to-excel
U download a picture of QRcode on a white background, and on the output it appears in excel file.


import ImageFilter
import pandas as pd
import openpyxl

from PIL import Image
filename = "your_picture.jpg"
with Image.open(filename) as img:
    img.load()

n = int(input())                                     #enter QRcode size

img.show()

gry = img.convert('L')
bw = gry.point(lambda x: 0 if x<128 else 255, '1')

MAX_SIZE = img.size

check = []

y = MAX_SIZE[1] - 1

a = 0

while y != 0:
    row = []
    for x in range (MAX_SIZE[0]):
        coordinate = x, y
        if bw.getpixel((coordinate)) == 0:
            row.append(0)
            a = 1
            break
        else:
            row.append(1)
    check.append(row)
    if a == 1:
        break
    y -= 1

x1, y1 = coordinate
print(x1,y1)
a = 0

for y in range (MAX_SIZE[1]):
    row = []
    for x in range (MAX_SIZE[0]):
        coordinate = x, y
        if bw.getpixel((coordinate)) == 0:
            row.append(0)
            a = 1
            break
        else:
            row.append(1)
    check.append(row)
    if a == 1:
        break

x1, y2 = coordinate

dif = y1 - y2
x2 = x1 + dif

print(x2, y2)
print(dif)
cropped = bw.crop((x1,(bw.height - y1), x2, (dif + y2)))

cropped.show()

array = []
for i in range(n):
    y = (round(cropped.height / (2*n))) + i * (round(cropped.height / n))
    row = []
    for j in range(n):
        x = (round(cropped.width / (2*n))) + j * (round(cropped.width / n))
        coordinate = x, y
        if cropped.getpixel((coordinate)) == 0:
            row.append(1)
        else:
            row.append(0)
    array.append(row)
    print(array[i])


df = pd.DataFrame(array)
df.to_excel(excel_writer = "test.xlsx")
