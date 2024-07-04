1. create new Google sheet

2. add all form input name in sheet data like below
name	email	phone	programme	year

3. click extensions + apps script

paste the below code in code.gs

var sheetName = 'data-sheet'; // same name as the sheet you added all name field head 
var scriptProp = PropertiesService.getScriptProperties();

function initialSetup() {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  scriptProp.setProperty('key', activeSpreadsheet.getId());
}

function doPost(e) {
  var lock = LockService.getScriptLock();
  lock.waitLock(10000); // Wait for up to 10 seconds for the lock

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
    var sheet = doc.getSheetByName(sheetName);
    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;

    var newRow = headers.map(function (header) {
      return header === 'timestamp' ? new Date() : (e.parameter[header] || '');
    });

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': err }))
      .setMimeType(ContentService.MimeType.JSON);
  } finally {
    lock.releaseLock();
  }
}

- click save + run (it will ask you for permission give all it want)

- inside Execution log there will we message like below
12:34:36 PM	Notice	Execution started
12:34:37 PM	Notice	Execution completed

- click deploy + new deployment

- In select type select web app 
- add description (project name)
- execute as select your Id
- who can access - anyone
- click deploy
- it will again for (The Web App requires you to authorize access to your data.)

- it should give you : Deployment ID, URL (copy and save them some where) 

4. Form page 

- create form like this
    <form name="sheet-form" method="post" class="mt-4">
                                <div class="mb-3">
                                    <input type="text" class="form-control" name="name" placeholder="Full Name">
                                </div>
                                <div class="mb-3">
                                    <input type="email" class="form-control" name="email" placeholder="Email ID">
                                </div>
                                <div class="mb-3">
                                    <input type="tel" class="form-control" name="phone" placeholder="Mobile No.">
                                </div>
                                <div class="mb-3">
                                    <select class="form-select" name="programme">
                                        <option selected>Select course you are interested in </option>
                                        <option value="B.Sc BIOTECHNOLOGY">B.Sc BIOTECHNOLOGY</option>
                                        <option value="MBA">MBA</option>
                                        <option value="M.Sc BIOTECHNOLOGY">M.Sc BIOTECHNOLOGY</option>
                                        <option value="BBA">BBA</option>
                                        <option value="PGDMA/GDMA">PGDMA/GDMA</option>
                                        <option value="B.COM">B.COM</option>
                                        <option value="BCA">BCA</option>
                                        <option value="BBA">BBA</option>
                                        <option value="B.Sc">B.Sc</option>
                                        <option value="BIOTECHNOLOGY">BIOTECHNOLOGY</option>
                                    </select>
                                </div>
                                <div class="mb-3">
                                    <input type="text" class="form-control" name="year" placeholder="Year of Passing">
                                </div>
                                <div class="hela-btn d-flex justify-content-between align-items-center">
                                    <button type="submit" name="submit" class="th-btn light">Get a call back</button>
                                    <a href="#" class="th-btn dark">Download brochure</a>
                                </div>
                            </form>

5. Js part 

 <script>
        const scriptURL = 'URL (that you get at the deployment time)';
        const form = document.querySelector('[name="sheet-form"]');
    
         form.addEventListener('submit', e => {
            e.preventDefault();
            fetch(scriptURL, { method: 'POST', body: new FormData(form) })
                .then(result => {
                    alert("Thanks for connecting with us! We will contact you soon.");
                    form.reset(); // Reset the form here
                })
                .catch(error => console.error('Error!', error.message));
        });
      </script>
      
- all set you are good to go  