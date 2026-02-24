# #917 - package com.example.freefiretournament

import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.freefiretournament.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.btnRegister.setOnClickListener {
            startActivity(Intent(this, RegisterActivity::class.java))
        }

        binding.btnTournaments.setOnClickListener {
            startActivity(Intent(this, TournamentListActivity::class.java))
        }
    }
} [Closed]

> Username: shabeer445  
> Created at: 2025-08-23 12:34:30 UTC  
> Updated at: 2025-08-23 15:08:31 UTC  
> Closed at: 2025-08-23 15:08:31 UTC  
> Url: https://github.com/boostorg/url/issues/917
