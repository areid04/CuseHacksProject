from __future__ import print_function
import PyPDF2
import re
from PyPDF2 import PdfReader
k = 10
def extract_text_from_pdf(pdf_path):
    with open(pdf_path, "rb") as f:
        reader = PdfReader(f)
        results = ""
        for i in range(0, len(reader.pages)):  # prev read.getNumPages()
            selected_page = reader.pages[i]
            text = selected_page.extract_text()
            results = results + text
        #print(type(results))
        #print(results[0])
        #print(results[1])
        #print(results[3])
        #derp = re.search("@syr.edu, results)
        #match = re.search(r"Jan ..(.:..)|Feb ..|Mar ..|Apr .. |May ..|June ..|July ..|Aug ..|Sept ..|Oct ..|Nov ..|Dec ..", results)
        #match1 = re.findall(r"Jan ..|Feb ..|Mar ..|Apr .. |May ..|June ..|July ..|Aug ..|Sept ..|Oct ..|Nov ..|Dec ..", results)
        #print(match)
        #dorp = results[(derp.start()-k):(derp.end())]
        #dorp = dorp.strip()
        #match = results[(match.start()):(match.end())]
        #print(dorp)
        #print(match)
        #print(match1)
        course = re.search(r"... \d\d\d",results)
        print(course)
        other = re.search(r"(Class|Course) Schedule| Calendar",results)
        other = results[other.start():(len(results)-1)]
        torp = results[course.start():course.end()]
        other = torp + " " +other
        print(other)
        return other  # convert list to a single doc
def make_list(text):
    match1 = re.findall(r"Jan ..|Feb ..|Mar ..|Apr .. |May ..|June ..|July ..|Aug ..|Sep. ..|Oct ..|Nov ..|Dec ..", text)
    if match1 == None:
        match4 = re.findall(r"January ..|February ..|March ..|April .. |May ..|June ..|July ..|August ..|September ..|October ..|Novovember ..|December ..",text)
        match4.append(text[0:6])
        print(match4)
        return match4
    match1.append(text[0:7])
    return match1
#def make_listnodates(text):
#    match1 = re.findall(r"Week (.ne|.wo|.hree|.our|.ive|.ix|.even|.ight|.ine|.en|.leven|.welve|.hirteen|.ourteen|.ifteen|.ixteen|\d\d|\d)",text)
#    return match1
def find_info(listo,text):
    to_split = []
    course = listo[-1]
    to_split.append(course.split(" "))
    listy = listo[:-1]
    for x in range(0, len(listy)):
        match = re.search(listy[x], text)
        if (x +1) <= (len(listy)-1):
            matchnext = re.search(listy[x+1], text)
            #if (((matchnext.start()) - (match.end())) > 50):
            #    to_split.append((text[match.start():match.end()], text[match.end():(match.end()+50)]))
                #print(text[match.start():(match.end()+50)])
            #else:
            to_split.append((text[match.start():match.end()].strip(), text[match.end():(matchnext.start())].strip()))
                #print(text[match.start():matchnext.start()])
        else:
            to_split.append((text[match.start():match.end()].strip(), text[match.end():(match.end()+50)].strip()))
            print(text[match.start():(match.end()+50)])
        #print(match.start())
        #print(match.end())
    return to_split

def cleanup(to_split):
    result = []
    for x in range(0, len(to_split)):
        print(to_split[x][0])
        print(to_split[x][1].strip())
        result.append((to_split[x][0],to_split[x][1].strip().replace("\n"," ")))
    date_first = []
    for x in range(0, len(result)):
        if re.search(r"Jan ..|Feb ..|Mar ..|Apr .. |May ..|June ..|July ..|Aug ..|Sep. ..|Oct ..|Nov ..|Dec ..", result[x][0]) == None:
            if (r"January ..|February ..|March ..|April .. |May ..|June ..|July ..|August ..|September ..|October ..|Novovember ..|December ..",result[x][0]) == None:
                date_first.append((result[x][1],re.sub(r" {4,}"," ", result[x][0])))
            else:
                date_first.append((result[x][0],re.sub(r' {4,}'," ",result[x][1])))
        else:
            date_first.append((result[x][0], re.sub(r' {4,}'," ",result[x][1])))
    return result


def combine(pdf_path):
    text = extract_text_from_pdf(pdf_path)
    listy = make_list(text)
    top = find_info(listy,text)
    clean = cleanup(top)
    return clean

def merp(location, date, activity):
    print(activity+" at "+location+" on "+date)


import datetime
import os.path

from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from google_auth_oauthlib.flow import InstalledAppFlow
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError

# If modifying these scopes, delete the file token.json.
SCOPES = ['https://www.googleapis.com/auth/calendar']


def addEvent(location, date, activity):
    """Shows basic usage of the Google Calendar API.
    Prints the start and name of the next 10 events on the user's calendar.
    """
    creds = None
    # The file token.json stores the user's access and refresh tokens, and is
    # created automatically when the authorization flow completes for the first
    # time.
    if os.path.exists('token.json'):
        creds = Credentials.from_authorized_user_file('token.json', SCOPES)
    # If there are no (valid) credentials available, let the user log in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                'credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        # Save the credentials for the next run
        with open('token.json', 'w') as token:
            token.write(creds.to_json())

    try:
        service = build('calendar', 'v3', credentials=creds)

        # Call the Calendar API
        now = datetime.datetime.utcnow().isoformat() + 'Z'  # 'Z' indicates UTC time
        print('Getting the upcoming 10 events')
        events_result = service.events().list(calendarId='primary', timeMin=now,
                                              maxResults=10, singleEvents=True,
                                              orderBy='startTime').execute()
        events = events_result.get('items', [])

        #if not events:
        #    print('No upcoming events found.')
        #    return

        # Prints the start and name of the next 10 events
        for event in events:
            start = event['start'].get('dateTime', event['start'].get('date'))
            print(start, event['summary'])

    except HttpError as error:
        print('An error occurred: %s' % error)

    created_event = service.events().quickAdd(
        calendarId='primary',
        text = activity+' at '+ location+' on '+date+" 2023").execute()

def loop(twopd):
    clean = combine(twopd)
    location = clean[0][0] + " "+ clean[0][1]
    clean = clean[1:]
    for x in range(0,len(clean)):
        date = clean[x][0]
        activity = clean[x][1]
        addEvent(location, date, activity)
    print("Done!")
# cleanup -> check for date in first posistion, fix
# iterate over a for loop, call merp xyz times when button is hit.
print("test")
# # from tkinter import *
# # from tkinter import messagebox
# # top = Tk()
# # entry = 
# # top.entry()

# # # C = Canvas(top, bg="blue", height=700, width=500)
# # # filename = PhotoImage(file="C:\\Users\\noahc\\VSCode\\background2.png")
# # # background_label = Label(top, image=filename)
# # # background_label.place(x=0, y=0)


# # # C.pack()

# # top.mainloop



# # # from tkinter import *

# # # class login(Tk):
# # #     def __init__(self):
# # #         super().__init__()
# # #         self.geometry("700x500")
# # #         self.resizable(False,False)
# # #     def Label(self):
# # #         Label.text = "asdfg"
# # #         # self.backGroundImage=PhotoImage(file="background2.png")
# # #         # self.backGroundImage=Label(self, image=self.backGroundImage)
# # #         # self.backGroundImage.place(x=0,y=0) 

# # # if __name__=="__main__":
# # #     Login=login()
# # #     Login.Label()
# # #     Login.mainloop()
    


# import tkinter as tk
# # from tkinter import messagebox

# window = tk.Tk()

# # C = Canvas(top, bg="blue", height=700, width=500)
# label = tk.Label(text="Please Enter File Name", fg="white", bg="black")
# entry = tk.Entry(fg="white", bg="black", width=50)

# # C.pack()
# label.pack()
# entry.pack()


# window.mainloop()

import tkinter as tk
from tkinter import filedialog




def UploadAction(event=None):
    filename = filedialog.askopenfilename()
    print('Selected:', filename)
    label = tk.Label(text = filename, fg="white", bg="black")
    label.pack()

    button = tk.Button(
    text="Look at your Calendar :)",
    width=25,
    height=5,
    bg="black",
    fg="white", command=loop(filename)  
    )
    button.bind("<Button-1>")
    button.pack()

root = tk.Tk()
button = tk.Button(root, text='Open', command=UploadAction)
button.pack()

# label = tk.Label(text = filename, fg="white", bg="black")

root.mainloop()
