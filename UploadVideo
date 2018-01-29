import android.support.annotation.NonNull;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Toast;

import com.google.android.gms.auth.api.signin.GoogleSignIn;
import com.google.android.gms.auth.api.signin.GoogleSignInAccount;
import com.google.android.gms.drive.Drive;
import com.google.android.gms.drive.DriveClient;
import com.google.android.gms.drive.DriveContents;
import com.google.android.gms.drive.DriveFile;
import com.google.android.gms.drive.DriveFolder;
import com.google.android.gms.drive.DriveResourceClient;
import com.google.android.gms.drive.MetadataChangeSet;
import com.google.android.gms.tasks.Continuation;

import com.google.android.gms.tasks.OnFailureListener;
import com.google.android.gms.tasks.OnSuccessListener;
import com.google.android.gms.tasks.Task;
import com.google.android.gms.tasks.Tasks;

import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;

import java.io.OutputStream;



public class UploadVideo extends AppCompatActivity {

    DriveClient mDriveClient;
    DriveResourceClient mDriveResourceClient;
    GoogleSignInAccount googleSignInAccount;
    String TAG = "Drive";
    private final int REQUEST_CODE_CREATOR = 2013;
    Task<DriveContents> createContentsTask;
    String uri;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_upload_video);
        //Fetching uri or path from previous activity.
        uri = getIntent().getStringExtra("uriVideo");
        //Get previously signed in account.
        googleSignInAccount = GoogleSignIn.getLastSignedInAccount(this);
        if (googleSignInAccount != null) {
            mDriveClient = Drive.getDriveClient(getApplicationContext(), googleSignInAccount);
            mDriveResourceClient =
                    Drive.getDriveResourceClient(getApplicationContext(), googleSignInAccount);
        }
        else Toast.makeText(this, "Login again and retry", Toast.LENGTH_SHORT).show();
        createContentsTask = mDriveResourceClient.createContents();
        findViewById(R.id.uploadVideo).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                    createFile();
            }
        });
    }

    private void createFile() {
        // [START create_file]
        final Task<DriveFolder> rootFolderTask = mDriveResourceClient.getRootFolder();
        final Task<DriveContents> createContentsTask = mDriveResourceClient.createContents();
        Tasks.whenAll(rootFolderTask, createContentsTask)
                .continueWithTask(new Continuation<Void, Task<DriveFile>>() {
                    @Override
                    public Task<DriveFile> then(@NonNull Task<Void> task) throws Exception {
                        DriveFolder parent = rootFolderTask.getResult();
                        DriveContents contents = createContentsTask.getResult();
                        File file = new File(uri);
                        ByteArrayOutputStream baos = new ByteArrayOutputStream();
                        byte[] buf = new byte[1024];
                        FileInputStream fis = new FileInputStream(file);
                        for (int readNum; (readNum = fis.read(buf)) != -1;) {
                            baos.write(buf, 0, readNum);
                        }
                        OutputStream outputStream = contents.getOutputStream();
                        outputStream.write(baos.toByteArray());

                        MetadataChangeSet changeSet = new MetadataChangeSet.Builder()
                                .setTitle("MyVideo.mp4") // Provide you video name here
                                .setMimeType("video/mp4") // Provide you video type here
                                .build();

                        return mDriveResourceClient.createFile(parent, changeSet, contents);
                    }
                })
                .addOnSuccessListener(this,
                        new OnSuccessListener<DriveFile>() {
                            @Override
                            public void onSuccess(DriveFile driveFile) {
                                Toast.makeText(Upload.this, "Upload Started", Toast.LENGTH_SHORT).show();
                                finish();
                            }
                        })
                .addOnFailureListener(this, new OnFailureListener() {
                    @Override
                    public void onFailure(@NonNull Exception e) {
                        Log.e(TAG, "Unable to create file", e);
                        finish();
                    }
                });
        // [END create_file]
    }
}
