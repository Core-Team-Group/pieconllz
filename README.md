  if (task.isSuccessful()) {  
          //Start The activity from here public class login extends AppCompatActivity {

private SignInButton signInButton;
private TextView textView;
private static final int RC_SIGN_IN = 1;
private GoogleApiClient mGoogleApiClient;
private FirebaseAuth mAuth;
private FirebaseAuth.AuthStateListener mAuthListener;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_login);

    mAuth = FirebaseAuth.getInstance();
    mAuthListener = new FirebaseAuth.AuthStateListener() {
        @Override
        public void onAuthStateChanged(@NonNull FirebaseAuth firebaseAuth) {
            if (firebaseAuth.getCurrentUser() != null) {
                Toast.makeText(getApplicationContext(), "Success", Toast.LENGTH_LONG).show();
                startActivity(new Intent(login.this, MainActivity.class));
            }
        }
    };

    signInButton = findViewById(R.id.SignIn);
    textView = findViewById(R.id.negativetext);
    textView.setText(getString(R.string.negativetext));

    textView.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            startActivity(new Intent(getApplicationContext(), MainActivity.class));
        }
    });

    GoogleSignInOptions gso = new GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
            .requestIdToken(getString(R.string.default_web_client_id))
            .requestEmail()
            .build();

    mGoogleApiClient = new GoogleApiClient.Builder(this)
            .enableAutoManage(this, new GoogleApiClient.OnConnectionFailedListener() {
                @Override
                public void onConnectionFailed(@NonNull ConnectionResult connectionResult) {
                    // Toast.makeText(getApplicationContext(), "Error", Toast.LENGTH_LONG).show();
                }
            })
            .addApi(Auth.GOOGLE_SIGN_IN_API, gso)
            .build();

    signInButton.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            signIn();
        }
    });

}


@Override
protected void onStart() {
    super.onStart();
    mAuth.addAuthStateListener(mAuthListener);
}

private void signIn() {
    Intent signInIntent = Auth.GoogleSignInApi.getSignInIntent(mGoogleApiClient);
    startActivityForResult(signInIntent, RC_SIGN_IN);
}

@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    // Result returned from launching the Intent from GoogleSignInApi.getSignInIntent(...);
    if (requestCode == RC_SIGN_IN) {
        Task<GoogleSignInAccount> task = GoogleSignIn.getSignedInAccountFromIntent(data);
        try {
            // Google Sign In was successful, authenticate with Firebase
            GoogleSignInAccount account = task.getResult(ApiException.class);
            //Toast.makeText(getApplicationContext(), ""+account, Toast.LENGTH_LONG).show();
            firebaseAuthWithGoogle(account);
        } catch (ApiException e) {
            // Google Sign In failed, update UI appropriately
            Log.w("Signing in: ", "Google sign in failed", e);
            // ...
        }
    }
}

private void firebaseAuthWithGoogle(GoogleSignInAccount acct) {
    AuthCredential credential = GoogleAuthProvider.getCredential(acct.getIdToken(), null);
    Toast.makeText(getApplicationContext(), "" + credential, Toast.LENGTH_LONG).show();
    //mAuth = FirebaseAuth.getInstance();
    mAuth.signInWithCredential(credential)
.addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {@Overridepublic void onComplete(@NonNull Task<AuthResult> task) {
                    Toast.makeText(getApplicationContext(), "" + task.isSuccessful(), Toast.LENGTH_LONG).show();
if (task.isSuccessful()) {// Sign in success, update UI with the signed-in user's information//Log.d(TAG, "signInWithCredential:success");
 //FirebaseUser user = mAuth.getCurrentUser();
//Log.i("Test:", " Compiled ?");
 //startActivity(new Intent(login.this, MainActivity.class));
                        Toast.makeText(getApplicationContext(), "Success", Toast.LENGTH_LONG).show();
                        startActivity(new Intent(login.this, MainActivity.class)); //updateUI(user);
} else {
                        Toast.makeText(getApplicationContext(), "Not Success", Toast.LENGTH_LONG).show();

 //Log.w(TAG, "signInWithCredential:failure", task.getException());
                        //Snackbar.make(findViewById(R.id.main_layout), "Authentication Failed.", Snackbar.LENGTH_SHORT).show();
                        //updateUI(null);
                    }

                    // ...
                }
            });

    // Toast.makeText(getApplicationContext(), ""+acct, Toast.LENGTH_LONG).show();

}startActivity(new Intent(login.this, MainActivity.class));
