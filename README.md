# Kisanhub-Assignment
import requests
from bs4 import BeautifulSoup
import time
t = requests.get("http://www.metoffice.gov.uk/climate/uk/summaries/datasets#Yearorder")
soup = BeautifulSoup(t.content, "lxml")
print cols
print content
main_table = soup.find_all("table")[0].find_all("tr")
i, file_type = 0, [" Max Temp"," Min Temp" ," Mean Temp"," Sunshine"," Rainfall"]
cities = ["UK", "England", "Scotland", "Wales"]
for row in main_table:
    if i == 0:
        i +=1
        pass
    
    else:
        cols = row.find_all("td")
        content = [ele.text.strip() for ele in cols]
        print content[0]
        if content[0] in cities:
            for j in range(1,6):
                link =  cols[j].find_all('a')[0]['href']
                data = 0
                while data <1:
                    try:
                        fcontent = requests.get(link)
                        data = 2
                    except:
                        time.sleep(3)
                fname = 'wdata/'+ str(content[0]) + str(file_type[j-1]) + '.txt' 
                open(fname, 'wb').write(fcontent.content)
             
i, file_type = 0, [" Max Temp"," Min Temp" ," Mean Temp"," Sunshine"," Rainfall"]
cities = ["UK", "England", "Scotland", "Wales"]

main_file = open("weather.csv","wb")
for coun in cities:
    for wtype in file_type:
        fname = 'wdata/'+str(coun) + str(wtype) + '.txt'
        file = open(fname)
        lines = file.readlines()
        results = {}
        month = ["JAN","FEB","MAR","APR", "MAY", "JUN","JUL", "AUG", "SEP","OCT","NOV","DEC", "WIN","SPR","SUM","AUT","ANN" ]
        for k in range(10, len(lines)):
            row = lines[k].split('   ')
            j = 0
            for i in row:
                if i == "":
                    pass
                else:
                    val =  i.split('  ')
                    year = str(val[-1]).strip(" ").strip('\n')
                    if year not in results.keys():
                        results[year] = {}
                    try:
                        results[year][month[j]] = val[-2]
                    except:
                        print k
                        results[year][month[j]] = 'N/A'
                    j +=1
        for i in sorted(results.keys()):
            for j in month:

                try:
                    main_file.write(str(coun) + ','+ str(wtype) + ','+ i + ','+ j + ','+ str(results[i][j]).strip(" ")+'\n' )
                except:
                    main_file.write(str(coun) + ','+ str(wtype) + ','+ i + ','+ j + ','+ 'N\A' + '\n')

main_file.close()
