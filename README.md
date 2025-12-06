import pickle

userid = None

#Functions for Binary File Operations
def dump(l):
    with open('movies.bin','wb') as f:
        for i in l:
            pickle.dump(i,f)
def load():
    l = []
    with open('movies.bin','rb') as f:
        try:
            while True:
                l.append(pickle.load(f))
        except EOFError:
            return l

#Functions for Rating Mechanism
def rate(d):
    sum = 0
    for i in d['rate'].values():
        sum+=i
    try:
        return sum/len(d['rate'])
    except ZeroDivisionError:
        return 0

def urate(d):
    global userid
    if userid in d['rate'].keys():
        return d['rate'][userid]
    else:
        return 'NULL'

#Functions for Authentication
def auth():
    print('\n')
    print('*'*40)
    print('IMDB')
    print('*'*40)
    print('\n')
    print('*'*40)
    print('Authentication')
    print('*'*40)
    print('1. SignIn\n2. Signup\n3. Exit')
    print('*'*40)
    ch = int(input('\n\n>>> Enter your Choice(1-3): '))
    if ch == 1: signin()
    elif ch == 2: signup()
    elif ch == 3: exit()
    else:
        print('Invalid Input\n\n')
        auth()
        
def signup():
    global userid
    n = input('\n\n>>> Username: ')
    p = input('>>> Password: ')
    with open('users.bin', 'rb') as f:
        try:
            while True:
                data = pickle.load(f)
                if data['Id'] == n:
                    print('User Id already Exist.')
                    auth()
        except EOFError:
            pass
    with open('users.bin', 'ab') as f:
        pickle.dump({'Id': n, 'passwd': p},f)
        userid = n
        print('Successfully SignedUp')
        
def signin():
    global userid
    n = input('\n\n>>> Username: ')
    p = input('>>> Password: ')
    with open('users.bin', 'rb') as f:
        try:
            while True:
                data = pickle.load(f)
                if (data['Id'] == n) and (data['passwd'] == p):
                    userid = n
                    print('Successfully SignedIn')
                    break
        except EOFError:
            print('Invalid Username or Password')
            auth()

#Admin Functions
def insert():
    d = {}
    d['name']  = input('>>> Enter the Movie Name:')
    d['year'] = int(input('>>> Enter the Year:'))
    d['date'] = input('>>> Enter the Date(DD/MM):')
    d['dur'] = input('>>> Enter the Duration(<Hours>h <Minutes>m):')
    d['cast'] = []
    while True:
        d['cast'].append(input('>>> Enter the cast(One by One):'))
        if input('>>> Do you Want to Enter More?(y/n): ') in 'Nn': break
    d['director'] = input('>>> Enter the Director Name:')
    d['boxoffice'] = int(input('>>> Enter the Box Office(USD):'))
    d['story'] = input('>>> Enter the Story:')
    d['genre'] = input('>>> Enter the Genre:')
    d['rate'] = {}
    with open('movies.bin','ab') as f:
        pickle.dump(d,f)
    print("Movie Successfully Added!")
    
def edit():
    name = input('\n\n>>> Enter the Movie Name:')
    l = load()
    for i in l:
        if (i['name'].lower() == name.lower()):
            d = {}
            d['name']  = i['name']
            d['year'] = int(input('>>> Enter the Year:'))
            d['date'] = input('>>> Enter the Date(DD/MM):')
            d['dur'] = input('>>> Enter the Duration(<Hours>h <Minutes>m):')
            d['cast'] = []
            while True:
                d['cast'].append(input('>>> Enter the cast(One by One):'))
                if input('>>> Do you Want to Enter More?(y/n): ') in 'Nn': break
            d['director'] = input('>>> Enter the Director Name:')
            d['boxoffice'] = int(input('>>> Enter the Box Office(USD):'))
            d['story'] = input('>>> Enter the Story:')
            d['genre'] = input('>>> Enter the Genre:')
            d['rate'] = i['rate']
            l[l.index(i)] = d
            dump(l)
            print("Movie : ",name,"modified Successfully!")
            break
    else:
        print("Movie does not exists!")

def delete():
    name = input('\n\n>>> Enter the Movie Name:')
    l = load()
    for i in l:
        if (i['name'].lower() == name.lower()):
            l.remove(i)
            dump(l)
            print("Movie : ",name," removed Successfully!")
            break
    else:
        print("Movie does not exists!")

#User Functions
#Functions for Search
def searchbyname():
    global userid
    name = input('\n\n>>> Enter the Movie Name: ')
    l = load()
    a = 0
    for i in l:
        if (i['name'].lower() == name.lower()):
            a += 1
            print('{:<20}'.format("\nMovie Name: "),i['name'],'{:<20}'.format("\nYear: "),i['year'],'{:<20}'.format("\nRelease Date: "),i['date'],'{:<20}'.format("\nDuration: "),i['dur'])
            print('Casts:')
            for j in i['cast']:
                print('*',j)
            print('{:<20}'.format("\nBox Office: "),i['boxoffice'],' USD','{:<20}'.format("\nDirector: "),i['director'],'{:<20}'.format("\nStory: "),i['story'],'{:<20}'.format("\nRatings: "),rate(i),'{:<20}'.format("\nYour Ratings: "),urate(i))
            if input('>>> Do you Want to Change Ratings of this Movie?(y/n): ') in 'Yy':
                i['rate'][userid] = int(input('Enter the Ratings(1-10): '))
    dump(l)
    print('\nLoaded {} results out of {}.'.format(a,len(l)))

def searchbygenre():
    global userid
    genre = input('\n\n>>> Enter the Genre: ')
    l = load()
    a = 0
    for i in l:
        if (i['genre'].lower() == genre.lower()):
            a += 1
            print('{:<20}'.format("\nMovie Name: "),i['name'],'{:<20}'.format("\nYear: "),i['year'],'{:<20}'.format("\nRelease Date: "),i['date'],'{:<20}'.format("\nDuration: "),i['dur'])
            print('Casts:')
            for j in i['cast']:
                print('*',j)
            print('{:<20}'.format("\nBox Office: "),i['boxoffice'],' USD','{:<20}'.format("\nDirector: "),i['director'],'{:<20}'.format("\nStory: "),i['story'],'{:<20}'.format("\nRatings: "),rate(i),'{:<20}'.format("\nYour Ratings: "),urate(i))
            if input('>>> Do you Want to Change Ratings of this Movie?(y/n): ') in 'Yy':
                i['rate'][userid] = int(input('Enter the Ratings(1-10): '))
    dump(l)
    print('\nLoaded {} results out of {}.'.format(a,len(l)))

def searchbyyear():
    global userid
    year = int(input('\n\n>>> Enter the Year: '))
    l = load()
    a = 0
    for i in l:
        if (i['year'] == year):
            a += 1
            print('{:<20}'.format("\nMovie Name: "),i['name'],'{:<20}'.format("\nYear: "),i['year'],'{:<20}'.format("\nRelease Date: "),i['date'],'{:<20}'.format("\nDuration: "),i['dur'])
            print('Casts:')
            for j in i['cast']:
                print('*',j)
            print('{:<20}'.format("\nBox Office: "),i['boxoffice'],' USD','{:<20}'.format("\nDirector: "),i['director'],'{:<20}'.format("\nStory: "),i['story'],'{:<20}'.format("\nRatings: "),rate(i),'{:<20}'.format("\nYour Ratings: "),urate(i))
            if input('>>> Do you Want to Change Ratings of this Movie?(y/n): ') in 'Yy':
                i['rate'][userid] = int(input('Enter the Ratings(1-10): '))
    dump(l)
    print('\nLoaded {} results out of {}.'.format(a,len(l)))
    
def searchbycast():
    global userid
    name = input('\n\n>>> Enter the Cast Name: ')
    l = load()
    a = 0
    for i in l:
        if (name in i['cast']):
            a += 1
            print('{:<20}'.format("\nMovie Name: "),i['name'],'{:<20}'.format("\nYear: "),i['year'],'{:<20}'.format("\nRelease Date: "),i['date'],'{:<20}'.format("\nDuration: "),i['dur'])
            print('Casts:')
            for j in i['cast']:
                print('*',j)
            print('{:<20}'.format("\nBox Office: "),i['boxoffice'],' USD','{:<20}'.format("\nDirector: "),i['director'],'{:<20}'.format("\nStory: "),i['story'],'{:<20}'.format("\nRatings: "),rate(i),'{:<20}'.format("\nYour Ratings: "),urate(i))
            if input('>>> Do you Want to Change Ratings of this Movie?(y/n): ') in 'Yy':
                i['rate'][userid] = int(input('Enter the Ratings(1-10): '))
    dump(l)
    print('\nLoaded {} results out of {}.'.format(a,len(l)))

def searchbyrating():
    global userid
    r = int(input('\n\n>>> Enter the Rating: '))
    l = load()
    a = 0
    for i in l:
        if(int(rate(i)) == r):
            a += 1
            print('{:<20}'.format("\nMovie Name: "),i['name'],'{:<20}'.format("\nYear: "),i['year'],'{:<20}'.format("\nRelease Date: "),i['date'],'{:<20}'.format("\nDuration: "),i['dur'])
            print('Casts:')
            for j in i['cast']:
                print('*',j)
            print('{:<20}'.format("\nBox Office: "),i['boxoffice'],' USD','{:<20}'.format("\nDirector: "),i['director'],'{:<20}'.format("\nStory: "),i['story'],'{:<20}'.format("\nRatings: "),rate(i),'{:<20}'.format("\nYour Ratings: "),urate(i))
            if input('>>> Do you Want to Change Ratings of this Movie?(y/n): ') in 'Yy':
                i['rate'][userid] = int(input('Enter the Ratings(1-10): '))
    dump(l)
    print('\nLoaded {} results out of {}.'.format(a,len(l)))

def search():
    while True:
        print('\n')
        print('*'*40)
        print('Search')
        print('*'*40)
        print('1. Search by Name\n2. Search by Genre\n3. Search by Year\n4. Search by Cast\n5. Search by Rating\n6. Exit')
        print('*'*40)
        ch = int(input('\n\n>>> Enter your Choice(1-6): '))
        if ch == 1: searchbyname()
        elif ch == 2: searchbygenre()
        elif ch == 3: searchbyyear()
        elif ch == 4: searchbycast()
        elif ch == 5: searchbyrating()
        elif ch == 6: break
        else: print('Invalid Input\n\n')

#Functions for Surfing/Browsing
def toprate():
    l = load()
    a = {}
    for i in l:
        a[rate(i)] = i
    b = sorted(a)
    print('\n\nTop Rated')
    for i in sorted(b[:5],reverse = True):
        print('{:<20}{:<20}{:<20}'.format(a[i]['name'],i,a[i]['genre']))
        
def bohits():
    l = load()
    a = {}
    for i in l:
        a[i['boxoffice']] = i
    b = sorted(a)
    print('\n\nBox Office Hits')
    for i in sorted(b[:5],reverse = True):
        print('{:<20}{:<20}{:<20}'.format(a[i]['name'],i,a[i]['genre']))
        
def mostviewed():
    l = load()
    a = {}
    for i in l:
        a[len(i['rate'])] = i
    b = sorted(a)
    print('\n\nBox Office Hits')
    for i in sorted(b[:5],reverse = True):
        print('{:<20}{:<20}{:<20}'.format(a[i]['name'],i,a[i]['genre']))
        
def surf():
    while True:
        print('\n')
        print('*'*40)
        print('Surf')
        print('*'*40)
        print('1. Top Ratings\n2. Box Office Hits\n3. Most Viewed\n4. Exit')
        print('*'*40)
        ch = int(input('\n\n>>> Enter your Choice(1-4): '))
        if ch == 1: toprate()
        elif ch == 2: bohits()
        elif ch == 3: mostviewed()
        elif ch == 4: break
        else: print('Invalid Input\n\n')

#Main pgm starts here.

auth()

while True:
    print('\n')
    print('*'*40)
    print('IMDB')
    print('*'*40)
    print('1. Surf\n2. Search\n3. Exit')
    if userid == 'admin':
        print('\n\n4. Insert\n5. Edit\n6. Delete.')
        print('*'*40)
        ch = int(input('\n\n>>> Enter your Choice(1-6): '))
        if ch not in [1,2,3,4,5,6]:
            print('Invalid Input')
            continue
    else:
        print('*'*40)
        ch = int(input('\n\n>>> Enter your Choice(1-3): '))
        if ch not in [1,2,3]:
            print('Invalid Input')
            continue
    if ch == 1: surf()
    elif ch == 2: search()
    elif ch == 3: break 
    elif ch == 4: insert() 
    elif ch == 5: edit()
    elif ch == 6: delete()
    else: print('Invalid Input\n\n')
    

    elif ch == 3: break 
    elif ch == 4: insert() 
    elif ch == 5: edit()
    elif ch == 6: delete()
    else: print('Invalid Input\n\n')
